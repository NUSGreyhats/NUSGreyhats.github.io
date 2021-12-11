---
author: "jh123x"
title: "Server Side Request Forgery"
date: "2021-12-12"
description: "An explaination on SSRF"
tags: ["writeups"]
ShowBreadcrumbs: False
---

## What is Server Side Request Forgery (SSRF)?
It is a web security vulnerability that allows an attacker to induce a server side application to make a HTTP request to a domain of the attacker's choosing.

Typically, the attacker will target the server's internal only services.

## Why SSRF?

<img src="./SSRF trend 2021.png">

Between 2017 to 2021, SSRF have been in the rise and is a new contender in the OWASP top 10. 

Some possible explainations might be the rise of the cloud and microservices architecture.

A large amount of information today is hosted on the cloud to improve deployment times, high application uptimes as well as autoscaling with technologies such as [kubernetes](https://kubernetes.io/).

With the microservices architecture, big services are split up into multiple smaller microservices where each microservice is used to maintain one function of the larger services. The microservices usually communicate with each other over HTTP or other lightweight protocols.

This microservices architecture provides a large surface for the attacker to attempt to exploit SSRF. Any Single vulnerable service will allow the attacked to access multiple microservices. With more services communicating with each other, the attacker will have a higher chance of finding a more impactful exploit.

## General Impact
The impact of SSRF is generally an attack on the server itself or other internal services that can be accessed from the server.

## How does SSRF work?

<img src="./SSRF diagram.png">

Although the attacker is unable to access the internal services directly, the attacker can still reach the other internal services through the vulnerable web servers.

In the diagram above, the attacker sends a malicious packet to the server to induce the server to make a request to other internal services (Either 1 or 2).

This usually occurs due to the lack of sanitization of user input especially when it comes to URLs. For example, if there is a web service that allows other users to convert a webpage into a PDF, the attacker can send the ip address of an internal service to view what is available on the other internal services.

An example of such a scenerio is [CVE-2020-7740](https://cve.mitre.org/cgi-bin/cvename.cgi?name=2020-7740) which affects all versions of `node-pdf-generator`.

In this case, there is no sanitization of the user's url before generating the PDF.

```javascript
function acceptHtmlAndProvidePdf(request, response) {
    console.log('Request received: ' + request);

    request.content = '';

    request.addListener("data", function (chunk) {
        if (chunk) {
            request.content += chunk;
        }
        // Lack of checks here
    });

    request.addListener("end", function () {

        var options = {
            encoding: 'utf-8',
            pageSize: request.headers['x-page-size'] || 'Letter'
        };

        response.writeHead(200, { 'Content-Type': 'application/pdf' });

        htmlToPdf(request.content, options)
            .pipe(response);

        console.log('Processed HTML to PDF: ' + response);
    });
}
```

This allows the attacker to forge a server request as the script fetches the information from the webpage to convert to PDF.

## What is the impact of SSRF
The impact of SSRF can widely vary depending on different circumstances.
1. Denial of Service
2. Remote Code execution
3. Bypassing access control
4. Service discovery


## Types of SSRF
There are mainly 2 different types of SSRF vulnerabilities.
1. Basic SSRF: The result is returned to the frontend and can be seen by the user.
2. Blind SSRF: The result of the attack is not returned to the frontend.

## Example of a Basic SSRF

An example of a basic SSRF exploit can be found [here](https://github.com/CS4239-U6/basic-ssrf)

In this example, the exposed service is running a PDF generation service. It takes in a URL from the user without sanitization and generates a PDF based on the link that it receives.

Within the same local environment, there is also another service that is running on `localhost:5001` that contains a web server that is only accessible internally.

By passing the url of this internal service into the PDF generation service, the attacker can generate a PDF of the webpage that is hosted on the internal service.


## Example of a Blind SSRF

An example of a blind SSRF exploit can be found [here](https://github.com/CS4239-U6/blind-ssrf)

In this example, the exposed service is a reporting server where users report suspicious attacks to the administrator.

The server automatically goes to the website and take a screenshot of the website before generating a markdown file for the admin to view.

Which this may seem like a good idea, a request from the server is forged in the process of taking the screenshot.

Although this is harder to exploit compared to the basic SSRF, it can still lead to remote code execution under correct circumstances.


## Comparison between Blind SSRF and Basic SSRF
| Criteria                                | Basic SSRF | Blind SSRF |
| --------------------------------------- | ---------- | ---------- |
| Can the attacker directly view the page | Yes        | No         |
| Difficulty of exploitation (In general) | Higher     | Lower      |



## References
1. [Port Swigger](https://portswigger.net/web-security/ssrf)
2. [OWASP Top 10](https://owasp.org/www-project-top-ten/)
3. [Wallarm](https://lab.wallarm.com/blind-ssrf-exploitation/)
