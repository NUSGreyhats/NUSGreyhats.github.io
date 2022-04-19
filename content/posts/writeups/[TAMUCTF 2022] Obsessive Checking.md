---
author: "rootkid"
title: "[TamuCTF 2022] Obsessive Checking"
date: 2022-04-18T12:38:31+08:00
description: "Reverse engineering using proxy, static and dynamic analysis"
tags: ["writeups", "ctf", "reverse engineer"]
ShowBreadcrumbs: False
---
Ported from my own blog https://rootkiddie.com/blog/post/ctf/tamu2022/obsessive-checking/writeup/

# Introduction

{{< figure src="/images/obsessive_checking/b20f0df70d73b5440fe11e16c900a625.png" >}}
This is a relatively challenging reverse engineering problem. Thankfully, the hint provided is very helpful. The link to the writeup for the challenge eBook DRM is https://ubcctf.github.io/2022/03/utctf-ebook-drm/. Strongly recommend a read as it is a very well written write up.

## Initial Analysis
Unzipping the challenge file, I was given 2 files. `obsessive-checking` and `flag_book.txt.bin`. Based the write-up above, I figured that this challenge also requires some way to proxy library functions to extract the key from obsessive-checking to decrypt the flag book file. However, unlike the UBCTF challenge, this challenge is written in RUST which added much more complexity to the analysis since it adds its own runtime and library to your code. Given that Rust is only starting to gain popularity in recent years, there's little information on Rust reverse engineering online. Therefore, I need to do a little exploration on my own.

### Finding the main method
The decompilation of the binary reveals a massive main function. 
{{< figure src="/images/obsessive_checking/1b1af6443b962d6f49ce7a7bf02e3509.png" >}}
However, I quickly realised that this is simply the setting up of the Rust runtime or sorts. The real main function can be found by ctrl-f and search for the keyword main.
{{< figure src="/images/obsessive_checking/676c693f4e9cc8102ab387c08532ec68.png" >}}
From this point on, main will refer to this obsessive_checking::main method instead of the ELF entry main method.

### Understanding the real main method
The decompilation of the main method shows 2492 lines in Ghidra. That's a massive function to analyse. Searching for strings did not result in any useful results either since the non-debugging output is only printed after decrypting the flag file. Therefore, I decided to use a little dynamic analysis to figure out where the main logic is. The trick I used here is to set breakpoints on printing functions and use backtraces to figure out where does the printing start. So, setting breakpoint on `core::fmt::write` and run it in gdb, I found where the user logic is ran.
{{< figure src="/images/obsessive_checking/b7444866a5c48c780d7f6d51d75f2e11.png" >}}
Looking at the decompilation in Ghidra, we have
{{< figure src="/images/obsessive_checking/3a3445c00ddd3e9b5fb9e378ceb5656d.png" >}}
From the stack-trace, we can see that the printing is done through the future poll. Based on my knowledge about Future from Java, it is an asynchronous composable structure that encloses program instructions. They are easy to read in code but a nightmare to reverse engineer as it's basically like a statically linked ELF with stripped symbols. But at this point, I already have a general idea on how this program work. Basically, the entire read file, delay, decrypt, print process is composed in Future and ran asynchronously. Now, we just need to figure out how to find the decryption key and the decryption algorithm.

## Figuring out the decryption

So out of the 4 steps (read, delay, decrypt, print), the only step that I am interested in is how the decryption is done. However, unlike the UBCTF where the binary is clearly using an openssl decryption function, this challenge does not seem to import any crypto libraries. That means, I need to figure out how the decryption structures are set up and hopefully extract the key from the structure initializations.

### The Proxy
During analysis of main function, I also realised that rust library uses memcpy to move datastructures around. Using the hint from UBCTF, I decided to proxy the memcpy function.
```c
#include <string.h>
#include <stdio.h>
#include <stdlib.h>

void *memcpy(void *dst, const void *src, size_t n) {
    printf("dst %p src %p size: %ld\n", dst, src, n);
    
    void *handle = dlopen("/usr/lib/x86_64-linux-gnu/libc-2.31.so", RTLD_NOW);
    void *(*orig_func)() = dlsym(handle, "memcpy");
    return orig_func(dst, src, n);
}
```
Compiling with `gcc -fPIC -shared OCDMeds.c -o OCDMeds.so` and running the binary with `LD_PRELOAD=./OCDMeds.so ./obsessive-checking flag_book.txt.bin` I have the following output.
```text
...
dst 0x7ffde6600d40 src 0x7ffde6600f20 size: 216
dst 0x7ffde6600880 src 0x7ffde6600d40 size: 216
dst 0x5598df52cb20 src 0x7ffde6600840 size: 296
dst 0x7ffde6601b01 src 0x5598df523d30 size: 16
dst 0x7ffde6601b01 src 0x5598df523d40 size: 16
dst 0x7ffde6601b01 src 0x5598df523d50 size: 16
dst 0x7ffde6601b01 src 0x5598df523d60 size: 16
dst 0x7ffde6601b01 src 0x5598df523d70 size: 16
dst 0x7ffde6601b01 src 0x5598df523d80 size: 16
dst 0x5598df52cce0 src 0x5598df52cc50 size: 80
dst 0x5598df52cd30 src 0x5598de995e4e size: 1
why yes, this is a string of output; unfortunately, it won't do you much good...
dst 0x5598df52cce0 src 0x5598de995e4f size: 0
dst 0x7ffde6601b01 src 0x5598df523d90 size: 16
dst 0x7ffde6601b01 src 0x5598df523da0 size: 16
dst 0x7ffde6601b01 src 0x5598df523db0 size: 16
dst 0x7ffde6601b01 src 0x5598df523dc0 size: 16
dst 0x7ffde6601b01 src 0x5598df523dd0 size: 16
dst 0x5598df52cce0 src 0x5598df52cc50 size: 80
dst 0x5598df52cd30 src 0x5598de995e4e size: 1
why yes, this is a string of output; unfortunately, it won't do you much good...
dst 0x5598df52cce0 src 0x5598de995e4f size: 0
dst 0x7ffde6601b01 src 0x5598df523de0 size: 16
dst 0x7ffde6601b01 src 0x5598df523df0 size: 16
dst 0x7ffde6601b01 src 0x5598df523e00 size: 16
dst 0x7ffde6601b01 src 0x5598df523e10 size: 16
dst 0x7ffde6601b01 src 0x5598df523e20 size: 16
...
```
Seeing the nice whole number 16, and the output string `why yes, this is a string of output; unfortunately, it won't do you much good...` is exactly 80 characters long, I knew that this is almost certainly a block cipher. The program is copying 5 blocks of cipher text from our flag file, decrypt them and print them to console before a delay is introduced. I also noted that the first 80-bytes block has an additional 16 bytes memcpy-ed which I assumed is the IV. 16 bytes IV? It seems like we are dealing with AES just like UBCTF. However, we can't be certain about that yet.
### The Better Proxy
To get more information from the proxy, I decided to make it better.
```c
#include <string.h>
#include <stdio.h>
#include <stdlib.h>
#include <dlfcn.h>

/* Paste this on the file you want to debug. */
#include <execinfo.h>
void print_trace(void) {
    char **strings;
    size_t i, size;
    enum Constexpr { MAX_SIZE = 1024 };
    void *array[MAX_SIZE];
    size = backtrace(array, MAX_SIZE);
    strings = backtrace_symbols(array, size);
    for (i = 0; i < size; i++)
        printf("%s\n", strings[i]);
    puts("");
    free(strings);
}

void *memcpy(void *dst, const void *src, size_t n) {
    printf("dst %p src %p size: %ld\n", dst, src, n);
    if (n == 16 || n==24 || n==32) {
        const char *buf = (const char*)src;
        for (int i=0; i<n ; i++) {
            printf("%02hhx", buf[i]);
        }
        puts("");
        for (int i=0; i<n; i++) {
            printf("%c", buf[i]);
        }
        puts("");
                print_trace();
    }

    void *handle = dlopen("/usr/lib/x86_64-linux-gnu/libc-2.31.so", RTLD_NOW);
    void *(*orig_func)() = dlsym(handle, "memcpy");
    return orig_func(dst, src, n);
}
```
And the output
```text
...

dst 0x55b3ad7a4470 src 0x7ffd70dff2a8 size: 136
dst 0x55b3ad7a80a0 src 0x55b3ad7aa0b0 size: 8192
dst 0x7ffd70e00821 src 0x55b3ad7a80a0 size: 16
509570e72f82ecb73d231a80abe75f57
Pp/=#_W
./OCDMeds.so(print_trace+0x47) [0x7f913d2d9280]
./OCDMeds.so(memcpy+0xe6) [0x7f913d2d940b]
./obsessive-checking(+0x17db8) [0x55b3abd4cdb8]
./obsessive-checking(+0x1912c) [0x55b3abd4e12c]
./obsessive-checking(+0x1f1fb) [0x55b3abd541fb]
./obsessive-checking(+0xf043) [0x55b3abd44043]
./obsessive-checking(+0x21bc7) [0x55b3abd56bc7]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xf3) [0x7f913cf630b3]
./obsessive-checking(+0xee8e) [0x55b3abd43e8e]

dst 0x7ffd70dff570 src 0x7ffd70e00638 size: 344
dst 0x7ffd70dffa70 src 0x7ffd70dffc50 size: 240
dst 0x7ffd70dff570 src 0x7ffd70dffa70 size: 480
dst 0x7ffd70e00070 src 0x7ffd70dff570 size: 960

...

dst 0x55b3ad7b1150 src 0x7ffd70dff570 size: 296
dst 0x7ffd70e00831 src 0x55b3ad7a80b0 size: 16
d8f602f6a56578b0eaa36d391b91cdc7
exm9
CDMeds.so(print_trace+0x47) [0x7f913d2d9280]
./OCDMeds.so(memcpy+0xe6) [0x7f913d2d940b]
./obsessive-checking(+0x17db8) [0x55b3abd4cdb8]
./obsessive-checking(+0x1b532) [0x55b3abd50532]
./obsessive-checking(+0x1f1fb) [0x55b3abd541fb]
./obsessive-checking(+0xf043) [0x55b3abd44043]
./obsessive-checking(+0x21bc7) [0x55b3abd56bc7]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xf3) [0x7f913cf630b3]
./obsessive-checking(+0xee8e) [0x55b3abd43e8e]

dst 0x7ffd70e00831 src 0x55b3ad7a80c0 size: 16
9e5d10ead375601989296a81c9fd55f2
]u`)jU
./OCDMeds.so(print_trace+0x47) [0x7f913d2d9280]
./OCDMeds.so(memcpy+0xe6) [0x7f913d2d940b]
./obsessive-checking(+0x17db8) [0x55b3abd4cdb8]
./obsessive-checking(+0x1b532) [0x55b3abd50532]
./obsessive-checking(+0x1f1fb) [0x55b3abd541fb]
./obsessive-checking(+0xf043) [0x55b3abd44043]
./obsessive-checking(+0x21bc7) [0x55b3abd56bc7]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xf3) [0x7f913cf630b3]
./obsessive-checking(+0xee8e) [0x55b3abd43e8e]


```
### Analysing the proxy output
From the better proxy, I found out that the obsessive-checking(+0x17db8) is in the read file function since both the IV and cipher blocks invoke this function. For initialization, it is most likely done in obsessive-checking(+0x1912c) and decryption in obsessive-checking(+0x1b532). Using either Ghidra / GDB (stepping after the read function), we can find out that the decryption algorithm used is AES-256. That means we need to look for a 32 bytes key. Checking the proxy log, we did not memcpy and 32 bytes structure. My guess is that the key is deterministically generated from a structure of different size and not easily extracted through memcpy proxy.

### Finding the Decryption Key
After realising that we are dealing with AES, I decided to take a look at which function I can set the breakpoint to extract the key. So I went to checkout the RUST AES implementation in https://github.com/RustCrypto/. After 20-30 minutes of reading the source code, I realised that KeyInit might be a good function to break at to extract the key since the argument is the Key itself which is represented in primitive Array. However, the function names are mangled and GDB does not automatically find where KeyInit method is. Using Ghidra analysing the function in obsessive-checking(+0x1912c), I found the address for keyinit invocation to be 0x55555556da95.
{{< figure src="/images/obsessive_checking/3c0ad5603cac668193c5f088661bfe0e.png" >}}

Setting breakpoint in GDB, I extracted the key as shown below.
{{< figure src="/images/obsessive_checking/b64c106540344a5eef79504c166ba6ad.png" >}}

## Decrypting the file
Now that we have the key and the algorithm, we can simply write a python script to decrypt the flag file and grep for the flag.
```python
from Crypto.Cipher import AES
from Crypto.Util.number import long_to_bytes
from pwn import *

def dec(key, iv, ct):
    cipher = AES.new(key, AES.MODE_CBC, iv)
    pt = cipher.decrypt(ct)
    return pt

key = p64(0xb10377e39a316bef)+p64(0x9e6b76de949612ec)+p64(0x5696f29e48ec594f)+p64(0xc6b3ed3f8c157327)

f = open("flag_book.txt.bin", "rb")
ct = f.read()
f.close()

with open("decryted.txt", "wb") as w:
    w.write(dec(key, ct[:16], ct))
```

FLAG: `gigem{round_and_round_and_round_it_goes_when_it_stops_checking_nobody_knows}`
