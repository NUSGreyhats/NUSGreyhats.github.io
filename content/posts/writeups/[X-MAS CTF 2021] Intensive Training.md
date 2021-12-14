---
author: "rootkid"
title: "[X-MAS CTF 2021] Intensive Training"
date: 2021-12-13T17:56:41+08:00
description: "Basic Reverse engineering challenge from X-MAS CTF 2021"
tags: ["writeups", "ctf", "reverse engineer"]
ShowBreadcrumbs: False
---

# Overview

This is a straightforward and classic reverse engineering challenge. It is a windows console application that will validate user input and checks the flag entered. I will go into a bit more details since this is a write up for beginners rather than experienced reverse engineers.

{{< figure src="/images/intensive_training/image-20211213205936274.png" >}}

# Understanding the target

The first step to reverse engineering is figuring out how the target binary is constructed. I don't want to waste our time reversing a packer or .NET bytecodes in IDA. So, I used Detect It Easy to check the binary.

{{< figure src="/images/intensive_training/image-20211213205722643.png" >}}

It's written in C++ which may have some mangled functions and complicated classes. But it is not that bad to reverse engineer in a classic decompiler like IDA or Ghidra.

# Decompile

Loading the binary in IDA, I can see most of the logic is actually written in the main method. That saved me a lot of time as there is not much structures or classes to worry about which tends to be the most time consuming part of reverse engineering a C++ program.

As the main method is fairly long, I will just copy paste the IDA decompilation output here.

```c++
int __cdecl main(int argc, const char **argv, const char **envp)
{
  // [COLLAPSED LOCAL DECLARATIONS. PRESS KEYPAD CTRL-"+" TO EXPAND]

  v3 = (char *)operator new(0x33ui64);
  v4 = sub_1400026E0(std::cout, (__int64)"Are you ready for [REDACTED]?");
  std::ostream::operator<<(v4, sub_1400028B0);
  std::istream::getline(std::cin, Str1, 40i64);
  if ( !strcmp(Str1, "nimic_interesant") )
  {
    strcpy_s(v3 + 34, 0x11ui64, Str1);          // v3[34]+ nimic_interesant
    memset(var_buf, 0, sizeof(var_buf));
    sub_140002240((__int64)var_buf);            // populate the var_buf
    if ( (*((_BYTE *)&var_buf[2] + *(int *)(var_buf[0] + 4)) & 6) != 0 )
      sub_1400026E0(std::cout, (__int64)"50 burpees");
    *(_OWORD *)Str1 = 0i64;
    v13 = 0i64;
    v14 = 0i64;
    std::istream::read(var_buf, Str1, 34i64);
    *(_OWORD *)v3 = *(_OWORD *)Str1;
    *((_OWORD *)v3 + 1) = v13;
    *((_WORD *)v3 + 16) = v14;
    v6 = v3[34];
    *v3 ^= v6;
    v3[1] ^= v3[35];
    v3[2] ^= v3[36];
    v3[3] ^= v3[37];
    v3[4] ^= v3[38];
    v3[5] ^= v3[39];
    v3[6] ^= v3[40];
    v3[7] ^= v3[41];
    v3[8] ^= v3[42];
    v3[9] ^= v3[43];
    v3[10] ^= v3[44];
    v3[11] ^= v3[45];
    v3[12] ^= v3[46];
    v3[13] ^= v3[47];
    v3[14] ^= v3[48];
    v3[15] ^= v3[49];
    v3[16] ^= v6;
    v3[17] ^= v3[35];
    v3[18] ^= v3[36];
    v3[19] ^= v3[37];
    v3[20] ^= v3[38];
    v3[21] ^= v3[39];
    v3[22] ^= v3[40];
    v3[23] ^= v3[41];
    v3[24] ^= v3[42];
    v3[25] ^= v3[43];
    v3[26] ^= v3[44];
    v3[27] ^= v3[45];
    v3[28] ^= v3[46];
    v3[29] ^= v3[47];
    v3[30] ^= v3[48];
    v3[31] ^= v3[49];
    v3[32] ^= v6;
    v3[33] ^= v3[35];
    v7 = 0i64;
    v8 = v3 - byte_140004508;
    while ( byte_140004508[v7] == byte_140004508[v7 + v8] )
    {
      if ( ++v7 >= 34 )
      {
        v9 = "Nice. Now go get your presents :D";
        goto LABEL_10;
      }
    }
    v9 = "Just a few more crunches";
LABEL_10:
    sub_1400026E0(std::cout, (__int64)v9);
    *(__int64 *)((char *)var_buf + *(int *)(var_buf[0] + 4)) = (__int64)&std::ifstream::`vftable';
    *(int *)((char *)&v10 + *(int *)(var_buf[0] + 4)) = *(_DWORD *)(var_buf[0] + 4) - 176;
    sub_140002190((__int64)&var_buf[2]);
    std::istream::~istream<char,std::char_traits<char>>(&var_buf[3]);
    std::ios::~ios<char,std::char_traits<char>>(&var_buf[22]);
    result = 0;
  }
  else
  {
    sub_1400026E0(std::cout, (__int64)"1000 pushups");
    result = -1;
  }
  return result;
}
```

# Analysis and solution

It is fairly obvious that after `"Are you ready for [REDACTED]?"` is printed, it will wait on user to provide an input. It will get the first line of the input and see if it is equal to "nimic_interesant". Then, it will continue to read 34 bytes from the input stream and store it in Str1. We will call these 34 bytes **user_input** and "nimic_interesant" the **key** from here on.

relevant code:

```c++
  v4 = sub_1400026E0(std::cout, (__int64)"Are you ready for [REDACTED]?"); // cout << "Are you ready for [REDACTED]?"
  std::ostream::operator<<(v4, sub_1400028B0);
  std::istream::getline(std::cin, Str1, 40i64); // get the first line.
  if ( !strcmp(Str1, "nimic_interesant") )
  {
    strcpy_s(v3 + 34, 0x11ui64, Str1);          // v3[34]+ nimic_interesant
    memset(var_buf, 0, sizeof(var_buf));
    sub_140002240((__int64)var_buf);            // populate the var_buf
    if ( (*((_BYTE *)&var_buf[2] + *(int *)(var_buf[0] + 4)) & 6) != 0 )
      sub_1400026E0(std::cout, (__int64)"50 burpees");
    *(_OWORD *)Str1 = 0i64;
    v13 = 0i64;
    v14 = 0i64;
    std::istream::read(var_buf, Str1, 34i64); // get the rest of the input
      ...
  }
```

This is followed by a large chunk of xor operations:



```c++
	v6 = v3[34];
    *v3 ^= v6;
    v3[1] ^= v3[35];
    v3[2] ^= v3[36];
    v3[3] ^= v3[37];
    v3[4] ^= v3[38];
    v3[5] ^= v3[39];
    v3[6] ^= v3[40];
    v3[7] ^= v3[41];
    v3[8] ^= v3[42];
    v3[9] ^= v3[43];
    v3[10] ^= v3[44];
    v3[11] ^= v3[45];
    v3[12] ^= v3[46];
    v3[13] ^= v3[47];
    v3[14] ^= v3[48];
    v3[15] ^= v3[49];
    v3[16] ^= v6;
    v3[17] ^= v3[35];
    v3[18] ^= v3[36];
    v3[19] ^= v3[37];
    v3[20] ^= v3[38];
    v3[21] ^= v3[39];
    v3[22] ^= v3[40];
    v3[23] ^= v3[41];
    v3[24] ^= v3[42];
    v3[25] ^= v3[43];
    v3[26] ^= v3[44];
    v3[27] ^= v3[45];
    v3[28] ^= v3[46];
    v3[29] ^= v3[47];
    v3[30] ^= v3[48];
    v3[31] ^= v3[49];
    v3[32] ^= v6;
    v3[33] ^= v3[35];
```

v3 is basically a 50 bytes buffer where the first 34 bytes are the **user_input** and the next 16 bytes are the **key**. It is essentially doing the following:

```python
for i in range (34):
	user_input[i] = user_input[i] ^ key[i%16]
```

Then, the while block following the xor operations:

```c++
v7 = 0i64;
v8 = v3 - byte_140004508;
while ( byte_140004508[v7] == byte_140004508[v7 + v8] )
{
    if ( ++v7 >= 34 )
    {
        v9 = "Nice. Now go get your presents :D";
        goto LABEL_10;
    }
}
```

This is using v7 as an index and v8 is the offset from v3 to byte_140004508. In a way, `&v3 == &byte_140004508[v8]`. Understanding that, we can see it is checking if the first 34 bytes in v3 is the same as the first 34 bytes in byte_140004508. The bytes turned out to be:

```
['0x36', '0x44', '0x20', '0x28', '0x30', '0x24', '0x27', '0x1', '0x3', '0x3a', '0xb', '0xa', '0x6', '0x46', '0x1c', '0x11', '0x31', '0x1b', '0x8', '0x8', '0x7', '0x26', '0x36', '0x8', '0x1b', '0x17', '0x2d', '0xd', '0x1c', '0x9', '0x1', '0x1c', '0x1', '0x14']
```

So, to get the expected input, we can use the following script

```python
key = "nimic_interesant"
expectedResult = ['0x36', '0x44', '0x20', '0x28', '0x30', '0x24', '0x27', '0x1', '0x3', '0x3a', '0xb', '0xa', '0x6', '0x46', '0x1c', '0x11', '0x31', '0x1b', '0x8', '0x8', '0x7', '0x26', '0x36', '0x8', '0x1b', '0x17', '0x2d', '0xd', '0x1c', '0x9', '0x1', '0x1c', '0x1', '0x14']
expectedInput = ""

for i in range(34):
    expectedInput += chr(int(expectedResult[i],16) ^ ord(key[i%len(key)]))

print (expectedInput)
```

And you will have the flag:

`X-MAS{Now_you're_ready_for_hohoho} `
