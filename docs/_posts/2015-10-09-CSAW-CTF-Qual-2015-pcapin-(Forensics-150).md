---
layout: post
title: CSAW CTF Qualifications 2015 - pcapin (Forensics 150)
author: anselm
description: CSAW CTF Qualifications 2015 (Forensics 150) - pcapin
tags: [CTF, Forensics, CSAW, PCAP]
category: [CTF, Forensics, PCAP]
excerpt: ""
share: true
comments: true
--- 
<h1>pcapin</h1>
We have extracted a pcap file from a network where attackers were present. We know they were using some kind of file transfer protocol on TCP port 7179. We're not sure what file or files were transferred and we need you to investigate. We do not believe any strong cryptography was employed.

Hint: The file you are looking for is a png <a href="/write-ups/resources/files/wu/csaw2015/pcapin_73c7fb6024b5e6eec22f5a7dcf2f5d82.pcap">pcapin_73c7fb6024b5e6eec22f5a7dcf2f5d82.pcap</a>
<hr/>

<p>
This challenge has surprisingly few solves. 41 out of 1000+ registered teams. We are given a pcap file with a few request/response from a client to a server and we have to figure out what is being sent.
</p>

<p>Analyzing the flow of the data being transferred, we can infer that two request are sent to the server and after each request, the server will send a series of packets as response.
</p>
<img src="/write-ups/resources/images/wu/csaw2015/pcapin-tcpstream.png" width="50%">

<h2>Request 1</h2>
<pre>
00:08:00:00:07:31:f9:e9
</pre>

<h2>Request 2</h2>
<pre>
00:3a:00:00:15:02:f9:e9:8f:95:88:9e:c7:89:87:9e:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9:e9:f9
</pre>
<p>
We see that request 1 is 8 bytes in length and coincidentally, there is also 00:08 in the data. Looking at the second request, we also see 00:3a in the same place. 3a is 58 in decimal and this correspond to the size of the request. Looking at the responses, we are also able to divide it into "packets" based on the size in the first 2 bytes. Another thing to note is that there appears to be an overwhelming amount of "f9:e9" in both the requests and in the first set of response from the server. This would infer that "f9:e9" is either the padding or the key since when XORed with null, it remains the same. We are able to verify that that is true by xoring with each of the packets in the first set of responses.
</p>

<pre>
document.pdf
sample.tif
outfile.dat
grey_no_firewall.zip
flag.png
resume.pdf
god.pcapng
malware.exe
</pre>

{% highlight python linenos %}
for cipher in response1:
	key = request1.split(":")
	key = key[6:]
	
	cipher = cipher.split(":")
	cipher = cipher[13:]

	ans = ""
	for i,c in enumerate(cipher):
		xor = (hexor(key[i % len(key)],c))
		ans += xor
	print ans[6:-4].decode("hex")
{% endhighlight %}

<p>
In the output, we noticed that there was a file named "flag.png". We can also see that the last 50 bytes of request 2 is identical to the section of cipher text that gets translated into "flag.png".
</p>
<img src="/write-ups/resources/images/wu/csaw2015/pcapin-flagcipher.png">

<P>
Thinking that we solved it, we processed all packets in response 2 the same way as how we solve response 1 but it resulted in gibberish data. Since we know that we need a png file, we then tried to "guess" the key by taking the data segment of the first packet in the response and xoring the first 8 bytes with the png header. Getting a series of "3f:50" back, we used it as a key but it again resulted in gibberish data. Undaunted, we again look deeper into the responses and noticed that they all have the same size. However, it is unlikely that the original data can be divided that nicely so it would probably be padded at the end. Looking at the last packet of response 2, we can see a series of "6c:4c" but that is also not the correct key. We then look for differences between packets in response 1 versus those in response 2 and noticed something unusual. In response 1, byte 3,4 are both "00:00" in all instances but in response 2, they are all different. 
</p>
<img src="/write-ups/resources/images/wu/csaw2015/pcapin-byte34.png" width="30%" height="30%">
<p>
Looking deeper, we realize that f9+45+1 = 13f -> 3f and e9+67=150 -> 50. Again verifying that with the last packet, f9+72+1=16c -> 6c and e9+63=14c -> 4c. Finally, using this new information, we found the flag!.
</p>
<img src="/write-ups/resources/images/wu/csaw2015/pcapin-flag.png">
<p>A s1mp!3_n37w0rk_c4@113nge that was not simple at all!</p>


{% highlight python linenos %}
for cipher in response2:
    cipher = cipher.split(":")

    key = ["f9","e9"]
    k1 = int(key[0],16)
    k2 = int(key[1],16)

    a1 = int(cipher[2],16)
    a2 = int(cipher[3],16)

    k1 = k1+a1+1
    k2 = k2+a2

    key = [hex(k2)[-2:],hex(k1)[-2:]]

    cipher = cipher[12:]

    ans = ""
    for i,c in enumerate(cipher):
        xor = (hexor(key[i % len(key)],c))
        ans += xor

    ans2 += ans

import binascii
hb=binascii.a2b_hex(ans2)
file = open("flag.png","wb")
file.write(hb)
file.close()
{% endhighlight %}

<p>Click <a href="/write-ups/resources/files/wu/csaw2015/pcapin_data_raw.txt">here</a> for the raw data found in the pcap.</p>
