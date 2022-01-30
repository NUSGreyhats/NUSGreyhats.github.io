---
author: "adhy-p"
title: "Introduction to Burp Suite"
date: "2022-01-30"
description: "What, why, and how of Burp"
tags: ["writeups"]
ShowBreadcrumbs: False
---

## What is Burp Suite?

Burp is all-in-one platform for website security testing. It has a variety of tools, such as:

- Proxy to intercept, inspect, and modify HTTP requests
- A repeater to easily edit and re-send HTTP requests
- An "intruder" to send multiple requests (one use case is to brute-force a login page)
- Text encoder/decoder (HTML, URL, Base64, etc.)

## Why Burp Suite?

When we are doing security testing, we want to give the application (lots of) unusual inputs. When we are dealing with website, these inputs are in the form of HTTP requests, be it `GET`, `POST`, or other type of requests. Burp allows us to easily modify and send these HTTP requests.

Burp also has a lot of advanced features (e.g. automatically scan the website for vulnerabilities), but that is a different topic and will not be covered in this writeup.

## Setting Up

You can download burp from [here](https://portswigger.net/burp).

Burp has its own built-in chromium browser, but you can
also configure it to work with external browsers like Chrome and Firefox. You may need to configure your browser proxy setting to
use burp, and you can find out how by going to [this link](https://portswigger.net/burp/documentation/desktop/external-browser-config). Additionally, you may want to install [FoxyProxy](https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-standard/) so you can easily change your browser's proxy setting.

\*In this writeup, we will try to attack online [labs](https://portswigger.net/web-security/) by PortSwigger. You may want to [create an account](https://portswigger.net/users/register) first before continuing.

## Using Burp Suite

In this writeup, we will cover the following:

- Proxy
- Repeater
- Intruder (and Turbo Intruder)

To do that, we will try to perform OS Comand Injection attack and login bruteforce attack.

## Proxy

> Burp Proxy is a web proxy server between your browser and target applications, and lets you intercept, inspect, and modify the raw traffic passing in both directions.

If you don't know what a proxy server is, [Wikipedia](https://en.wikipedia.org/wiki/Proxy_server) gives a pretty good explanation. Instead of sending HTTP request directly to the target, your browser will send the request to the proxy server and the proxy server will forward (or edit/drop) the request to the target.

![proxy](https://upload.wikimedia.org/wikipedia/commons/thumb/b/bb/Proxy_concept_en.svg/416px-Proxy_concept_en.svg.png)

First, open burp and go to the `Proxy` tab. You will see something like this:
![first-landing](/images/intro-to-burp/first-landing.png)
You can click `Open Browser` to use Burp's built-in browser, or open your own browser if you have configured the proxy settings. Make sure the `intercept` option is on.

Now, try to go to any website. Your browser should hang and you can see this in your Burp:
![intercept](/images/intro-to-burp/intercepted-request.png)
Here, the browser sent a GET request to Burp, but we have not forwarded the request. That's why we don't see anything loaded in our browser. At this stage, we can try to tinker around and change the request header to `OPTIONS` or `POST`, or we can change the cookie and add new data inside the request body. After we are done editing the request, we can press `Forward` and the request will be sent to the target.

Note:

> If you are trying to access sites like Yahoo or Youtube, you can get hundreds of HTTP requests, and manually clicking `Forward` can be tiring. You can turn off the `intercept` option and all requests will be forwarded automatically. You can always review the requests you have sent from the `HTTP History` tab.

### Repeater

> Burp Repeater is a simple tool for manually manipulating and reissuing individual HTTP and WebSocket messages, and analyzing the application's responses. You can use Repeater for all kinds of purposes, such as changing parameter values to test for input-based vulnerabilities, issuing requests in a specific sequence to test for logic flaws, and reissuing requests from Burp Scanner issues to manually verify reported issues.

To begin, turn off the intercept option from the previos section and go to https://portswigger.net/web-security/os-command-injection/lab-simple to access the lab. Note that even when our intercept is turned off, burp will still record all HTTP requests it forwarded.

According to the lab description, the application contains an OS command injection vulnerability in the product stock checker. Let's try to see a product and check its stock:
![stock](/images/intro-to-burp/stock.png)

After we clicked the button, we can see the stock of the product, but nothing more. Now, turn on the intercept option and try to check the stock again. We can see that our browser is actually sending a `POST` request to the app.
![post-req](/images/intro-to-burp/post-req.png)
We can play with the store ID. We can put `whoami`, `123456`, etc. But, this process of going to the page, intercepting the request, changing the request, and then forwarding the request is very cumbersome. Let's find another way.

Let's see the HTTP history:
![history](/images/intro-to-burp/history.png)
Now, right click on the request, then select `Send to repeater`.

![send-to-repeater](/images/intro-to-burp/send-to-repeater.png)

![whoami](/images/intro-to-burp/whoami.png)
Here, we can send the `POST` request to the server again and again by changing the request and clicking `Send`. There's no need for us to go to the page, intercept the request, change the request, and forward the request again and again.

To solve the lab, close the current command with a semicolon and inject `whoami`. The username will be printed on the Response tab.
![whoami-res](/images/intro-to-burp/whoami-res.png)

### Intruder

> Burp Intruder is a tool for automating customized attacks against web applications. It is extremely powerful and configurable, and can be used to perform a huge range of tasks, from simple brute-force guessing of web directories through to active exploitation of complex blind SQL injection vulnerabilities.

To demonstrate this feature, we will try to solve https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-different-responses.

We are given a list of possible usernames and passwords, and our job is to login to the platform.

Of course, we can try to manually try every single combination from the web page, or use the repeater to directly edit the HTTP request. But, it will take a lot of time if we have to do this manually.
![admin-password](/images/intro-to-burp/admin:password.png)

What we can do, is to right click on the login request and send it to intruder.
![send-to-intruder](/images/intro-to-burp/send-to-intruder.png)
There are four different modes of intruder: `sniper`, `Battering ram`, `Pitchfork`, and `Cluster bomb`. We will use `sniper` mode for this example and will not cover the difference between those attack types, but you can read more [here](https://www.sjoerdlangkemper.nl/2017/08/02/burp-intruder-attack-types/) if you want to know more.

First, go to `Positions` tab and clear all markers. Then, add a marker on the `username` field.
![clear-marker](/images/intro-to-burp/clear-marker.png)
![add-marker](/images/intro-to-burp/add-marker.png)
Next, go to the `Payload` tab and paste the wordlist from the lab website.
![paste-usernames](/images/intro-to-burp/paste-usernames.png)
Finally, click `Start attack` to initiate the attack. This will take a while if you are using the free (community) version.

After the attack is finished, sort the response according to its length, and we can see that one response has different length. Indeed, this is the correct username we are looking for.
![intruder-result](/images/intro-to-burp/intruder-result.png)
Now, we put the correct username, add a marker for the password tab, and repeat the process again to get the password.

### Turbo Intruder

If you are using Burp community edition, you will realize that the intruder is really slow. Its rate is limited to around one request per second. So, if you have thousands of requests to be sent, it can take forever. Luckily, we have an alternative called Turbo Intruder. To use it, we must first install it from the BApp store under the `Extender` tab.
![bapp](/images/intro-to-burp/bapp-turbo-intruder.png)

Then, from our HTTP history tab, instead of sending the packet to intruder, we select `Extensions` and `Send to Turbo Intruder`
![send-to-turbo](/images/intro-to-burp/send-to-turbo.png)

Turbo intruder uses python, so you can really customize your own code if you understand Python's syntax. We save the wordlist from the lab website locally, and then we put a format string specifier `%s` to indicate the string we want to change.
![turbo-python](/images/intro-to-burp/turbo-python.png)
You can change the number of threads used to send the requests, as well as the number of requests for each connection. A word of caution, if you set these numbers wrongly, you can overload and crash the server and/or crash your own computer (most probably your computer will crash before the server does though). Also, in some cases, you want to keep these numbers low because the server may reject the connection if it has a rate limiting algorithm in place.

Setting the number of threads to be 5 and the number of connections to be 1, we can achieve around 9 requests per second, which is 9 times better than the original burp intruder.
![turbo-result](/images/intro-to-burp/turbo-result.png)

## Moving forward

Now that you know the basic, the next thing to do is to practice :)

You can start by solving the challenges in these websites:

- [DVWA](https://github.com/digininja/DVWA)
- [WebGoat](https://owasp.org/www-project-webgoat/)
- [PortSwigger Academy](https://portswigger.net/web-security)

Then, if you are interested with pentesting real sites, you can read [Web Hacking 101](http://leanpub.com/web-hacking-101) by Peter Yaworski and go to [Hackerone](https://www.hackerone.com/) and try to find some real vulnerabilities. Have fun!

## References

- https://portswigger.net/burp
- https://portswigger.net/burp/documentation/desktop/tools/proxy/getting-started
- https://en.wikipedia.org/wiki/Proxy_server
- https://portswigger.net/burp/documentation/desktop/tools/repeater/using
- https://portswigger.net/burp/documentation/desktop/tools/intruder/using
- [Burp intruder attack types](https://www.sjoerdlangkemper.nl/2017/08/02/burp-intruder-attack-types/)
- [Turbo Intruder](https://portswigger.net/bappstore/9abaa233088242e8be252cd4ff534988)
