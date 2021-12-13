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

{{< figure src="/images/ssrf/SSRF trend 2021.png" title="SSRF trend" >}}

Between 2017 to 2021, SSRF have been in the rise and is a new contender in the OWASP top 10. 

Some possible explainations might be the rise of the cloud and microservices architecture.

A large amount of information today is hosted on the cloud to improve deployment times, high application uptimes as well as autoscaling with technologies such as [kubernetes](https://kubernetes.io/).

With the microservices architecture, big services are split up into multiple smaller microservices where each microservice is used to maintain one function of the larger services. The microservices usually communicate with each other over HTTP or other lightweight protocols.

This microservices architecture provides a large surface for the attacker to attempt to exploit SSRF. Any single vulnerable service will allow the attacker to access multiple microservices. With more services communicating with each other, the attacker will have a higher chance of finding a more impactful exploit.

## General Impact
The impact of SSRF is generally an attack on the server itself or other internal services that can be accessed from the server.

## How does SSRF work?

{{< figure src="/images/ssrf/SSRF diagram.png" title="SSRF" >}}

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
4. Port Scanning: Reqeusts can be made to different ports and the resulting status code or result shown on the frontend allows the attacker to infer if the port is open
5. Other internal services (Details on the attacks are given in the references below)
   1. Redis: If Redis uses a text based protocol (RESP), the attacker can send a payload with the correct format and send commands to the redis server. 
   2. Cloud metadata: Metadata API base url can be given to the vulnerable server to retrieve information about the server. 


## Types of SSRF
There are mainly 2 different types of SSRF vulnerabilities.
1. Basic SSRF: The result is returned to the frontend and can be seen by the user.
2. Blind SSRF: The result of the attack is not returned to the frontend.
3. Semi-Blind SSRF: The attacker only knows if the payload was successful or not. No details are given.

## Comparison between Different Types of SSRF
| Criteria                                | Basic SSRF | Blind SSRF | Semi-Blind SSRF |
| --------------------------------------- | ---------- | ---------- | --------------- |
| Can the attacker directly view the page | Yes        | No         | No              |
| Difficulty of exploitation (In general) | Lower      | Higher     | Medium          |

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


## Mitigations for SSRF
1. Input validation (Sanitization) for URLs given by the user.
   1. This can be in the form of a whitelist or a blacklist (There is a different list of caveats for blacklists)
   2. Verification that IP / Domain is not an internal IP address / invalid address.
2. Do not accept complete URLs from users.
3. Firewall filters to prevent access of unauthorised domains


## Bypasses for Mitigations

However, for each the mitigations, there might be some bypasses which can be used to reduce the effectiveness of the mitigations.

1. Usage of malformed URLs
   1. `{domain}@127.0.0.1` or `127.0.1` all redirects to `localhost`. There are multiple encodings of this url. Similar methods can be used for other urls.
2. DNS rebinding
   1. The attacker can register a domain and point it to a non-blacklisted website.
   2. After the server checks that the domain is valid, the attacker can change it to point to an internal ip address.
   3. When the server visits the domain again, the server will visit the actual website.
   4. More information can be found below
3. Open Redirect
   1. If there is another open redirection on the page,  the open redirection can be used to bypass restrictions on the webpage.
4. Bypass via Redirection
   1. There might be filtering when it comes to a URL.
   2. This can be used to bypass url filters by registering a valid url which bypasses the various types of filtering.
5. SSRF via Referrer header
   1. Sometimes web applications make use of server side analytics software that tracks visitors. These software logs the referrer header in the request and actually visit the websites to analyze the contents of referrer sites.


## References
1. [Port Swigger](https://portswigger.net/web-security/ssrf)
2. [OWASP Top 10](https://owasp.org/www-project-top-ten/)
3. [Wallarm](https://lab.wallarm.com/blind-ssrf-exploitation/)
4. [Attacking Redis Through SSRF](https://infosecwriteups.com/exploiting-redis-through-ssrf-attack-be625682461b)
5. [SSRF Exposes data of technology](https://unit42.paloaltonetworks.com/server-side-request-forgery-exposes-data-of-technology-industrial-and-media-organizations/)
6. [From SSRF to port scanner](https://cobalt.io/blog/from-ssrf-to-port-scanner)
7. [Hacktricks - SSRF Bypasses](https://book.hacktricks.xyz/pentesting-web/ssrf-server-side-request-forgery)
8. [SSRF Bypass Cheatsheet](https://highon.coffee/blog/ssrf-cheat-sheet/)
9. [SSRF DNS Rebinding](https://geleta.eu/2019/my-first-ssrf-using-dns-rebinfing/)
10. [SSRF Bypass techniques](https://bitthebyte.medium.com/popularizing-some-ssrf-techniques-for-fun-and-profit-1d11b6321744)
