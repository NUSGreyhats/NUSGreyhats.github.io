---
layout: post
title: ASIS CTF Finals 2015 - Big Lie (Forensics 100)
author: quanyang
description: ASIS CTF Finals 2015 Forensics 100. Big Lie.
tags: [CTF, Forensics, ASIS, PCAP]
category: [CTF, Forensics, PCAP]
excerpt: ""
comments: true
share: true
--- 

ASIS CTF 2015 Finals just took place over the weekend of 10,11 October 2015. The finals is open to all, however only qualified teams will be allowed to win the prizes. NUS Greyhats took part in it and solved a few challenges, this is our write-up for some of the challenges from ASIS CTF 2015 Finals. 

# Big Lie
**Points:**100
**Category:** Forensics

Find the [flag]({{ site.url }}/resources/files/asis/biglie.pcap).

MD5: c3037269053e61e10a2a2457051519c8

---

# Our solution

We are given a pcap file and asked to find the flag. Opening up the pcap file with Wireshark, we then see many HTTP traffic conversations in it.

So, the first thing to do is to filter away redundant packets. I am only looking for HTTP requests URI with the word "asis" in it.
`http.request.uri contains asis` 

![]({{site.url|append: site.baseurl}}/resources/images/asis/biglie/wireshark.png){: height="500px" width="auto"}

We can see this this weird piwik.php request made to a HTTP server, and there seems to be a pastebin url in the GET request.
`http://0bin.asis.io/paste/Vyk5W274#1L8OT3oT7Xr0ryJlS5ASprAqgsQysKeebbSK90gGyQo`
![]({{site.url|append: site.baseurl}}/resources/images/asis/biglie/interesting.png){: height="200px" width="auto"}

Looking at all similar conversations, I found 3 such conversations.

The first one is:
`http://0bin.asis.io/paste/TINcoc0f#-krvZ7lGwZ4e2JQ8n+3dfsMBqyN6Xk6SUzY7i0JKbpo`
![]({{site.url|append: site.baseurl}}/resources/images/asis/biglie/first.png){: height="200px" width="auto"}

The second one is:
`http://0bin.asis.io/paste/Vyk5W274#1L8OT3oT7Xr0ryJlS5ASprAqgsQysKeebbSK90gGyQo`
![]({{site.url|append: site.baseurl}}/resources/images/asis/biglie/second.png){: height="200px" width="auto"}

The third one which gave us our flag is:
`http://0bin.asis.io/paste/1ThAoKv4#Zz-nHPnr0vGGg3s/7/RWD2pnZPZl580x9Y2G3IUehfc`
![]({{site.url|append: site.baseurl}}/resources/images/asis/biglie/preflag.png){: height="200px" width="auto"}

Throwing this into a text editor gives me:
![]({{site.url|append: site.baseurl}}/resources/images/asis/biglie/flag.png){: height="auto" width="auto"}

And we have our flag: **ASIS{e29a3ef6f1d71d04c5f107eb3c64bbbb}**