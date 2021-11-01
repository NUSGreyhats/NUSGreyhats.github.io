---
author: "daniellimws"
title: "Basic LLDB Scripting"
date: "2021-11-01"
description: "Basic knowledge to get started with LLDB scripting"
tags: ["writeups"]
ShowBreadcrumbs: False
---

During my internship, I reverse engineer macOS programs. So, I need a debugger.

I am quite familiar with GDB, and there are nice extensions like GEF out there. However, Apple made it so difficult to compile a usable GDB on macOS... 😑 We are pretty much forced to use LLDB (which I really don't like).

Anyways, beggars can't be choosers, and vanilla GDB/LLDB is not really usable in the long run, so being able to use the scripting interface is very important. Similar to GDB, LLDB also has a Python scripting interface. Instead of **.gdbinit**, LLDB loads commands from **.lldbinit** during startup. There is a great **lldbinit** project that contains many custom commands which makes LLDB so much nicer to use, and I have a [fork of it](https://github.com/daniellimws/lldbinit).

(It is such a pain to even set breakpoints, vanilla LLDB is just not usable imo.)

## LLDB Architecture and Python Bindings

Having the lldbinit extension is good. Being able to add my own custom commands is even better. To do so, I had to understand the architecture of the scripting interface.

The LLDB scripting interface is quite tidy. Everything is grouped into modules. Quoting the docs, here are less than half of the modules:

* `SBAddress`
  * A section + offset based address class.
* `SBBreakpoint`
  * Represents a logical breakpoint and its associated settings.
* `SBBreakpointList`
  * Proxy of C++ `lldb::SBBreakpointList` class
* `SBBreakpointLocation`
  * Represents one unique instance (by address) of a logical breakpoint.
* `SBCommandInterpreter`
  * `SBCommandInterpreter` handles/interprets commands for lldb.
* `SBCommandReturnObject`
  * Represents a container which holds the result from command execution.

Feels like writing C++ but in Python. And the basic architecture is as follows:

```
LLDB design:
------------|
lldb -> debugger -> target -> process -> thread -> frame(s)
                                      -> thread -> frame(s)
```

- `LLDB` talks to the `debugger` object
- `debugger` holds a `target`
- `target` holds a `process`
- `process` holds multiple `threads`
- and lastly, each `thread` has one or more `frame`s

With more details (quoting the docs):
- `SBTarget`: Represents the **target program** running under the debugger.
  - Gives information about the **executable**, **process**, **modules**, **memory**, **breakpoints**, etc
  - There's a lot, check the [docs](https://lldb.llvm.org/python_reference/lldb.SBTarget-class.html)
- `SBProcess`: Represents the **process** associated with the target program.
  - Gives information about the **process**, **memory**
  - Some overlap with the above, but this one doesn't have **modules** nor **breakpoints**
  - Check the [docs](https://lldb.llvm.org/python_reference/lldb.SBProcess-class.html)
- `SBThread`: Represents a **thread** of execution.
  - Gives information about a **thread**, e.g. thread ID
  - Exposes functions for **stepping** (step in, step over, etc) and **suspending/resuming**
  - Contains stack frame(s) (according to docs, it is possible to have more than 1, but I always see just 1)
  - Check out the [docs](https://lldb.llvm.org/python_reference/lldb.SBThread-class.html)
- `SBFrame`: Represents one of the **stack frames** associated with a thread.
  - Gives information about a **stack frame**, e.g. **registers**, **functions**, **symbols**, **disassembly**, etc
  - This is a really useful module because of the information it gives.
  - Check out the [docs](https://lldb.llvm.org/python_reference/lldb.SBFrame-class.html)

Now, some useful functions to access the objects mentioned above (defined by **lldbinit**):
- `SBTarget` - `get_target()`
- `SBProcess` - `get_process()`
- `SBThread` - `get_thread()`
- `SBFrame` - `get_frame()`

Yea, quite easy.

## Create Custom Commands

To define a new command, it is as simple as creating a function in **lldbinit.py**. For example, to create a command called `newcmd`:

```py
def cmd_newcmd(debugger, command, result, _dict):
    args = command.split(' ')
    if len(args) < 1:
        print('newcmd <expression>')
        return

    ...
```

Then, the function must be registered as a command in the `__lldb_init_module` method:

```py
def __lldb_init_module(debugger, internal_dict):
    ...
    ci.HandleCommand("command script add -f lldbinit.cmd_newcmd newcmd", res)
    ...
```

The function name can be anything, but by convention all command functions in lldbinit go by `cmd_<command name>`.

The function arguments `result` and `_dict` might be useful but I don't use them. `debugger` is the LLDB debugger object mentioned earlier, and `command` is the exact command string entered by the user.

We can use the API listed above to obtain information about the **target/process/thread/frame**, or perform actions such as **setting breakpoints**, **stepping through instructions**, etc.

### Example 1: Reading Memory

Here's a simple command I wrote to print unicode strings from memory (similar to `x/s` but printing unicode strings).

```py
def cmd_xu(debugger, command, result, _dict):
    args = command.split(' ')
    if len(args) < 1:
        print('xu <expression>')
        return

    addr = int(get_frame().EvaluateExpression(args[0]).GetValue(), 10)
    error = lldb.SBError()

    ended = False
    s = u''
    offset = 0

    while not ended:
        mem = get_target().GetProcess().ReadMemory(addr + offset, 100, error)
        for i in range(0, 100, 2):
            wc = mem[i+1] << 8 | mem[i]
            s += chr(wc)
            if wc == 0:
                ended = True
                break

        offset += 100

    print(s)
```

### Example 2: Alias

It is also possible to make aliases for long commands. For example, an alias for disabling breakpoints, through the `SBCommandInterpreter` object obtained via the `debugger.GetCommandInterpreter()` method.

```py
# disable breakpoint number
def cmd_bpd(debugger, command, result, dict):
    res = lldb.SBCommandReturnObject()
    debugger.GetCommandInterpreter().HandleCommand("breakpoint disable " + command, res)
    print(res.GetOutput())
```

### Example 3: Function Tracing

Lastly, here is an example of a more complicated command I wrote to get the list of functions called by the target. This is useful when I am attaching LLDB to Safari, and want to know the functions in a library that were called by Safari when loading a webpage.

For example, to see the functions in `CoreGraphics` called when browsing Wikipedia:

```
(lldbinit) cz CoreGraphics
[+] Creating breakpoints for all symbols in CoreGraphics
[+] Done creating breakpoints for all symbols in CoreGraphics
0x7fff2505d324:
| CoreGraphics CGColorSpaceUsesExtendedRange
|__ WebKit WebKit::ShareableBitmap::calculateBytesPerRow(WebCore::IntSize, WebKit::ShareableBitmap::Configuration const&)
0x7fff2504a997:
| CoreGraphics CGColorSpaceGetNumberOfComponents
|__ QuartzCore CABackingStorePrepareUpdates_(CABackingStore*, unsigned long, unsigned long, unsigned int, unsigned int, unsigned int, unsigned long long, CA::GenericContext*, UpdateState*)
0x7fff250496fc:
| CoreGraphics CGColorSpaceRetain
|__ QuartzCore CA::CG::IOSurfaceDrawable::IOSurfaceDrawable(__IOSurface*, unsigned int, unsigned int, CGColorSpace*, int, int, unsigned int, unsigned int)
0x7fff2549cd24:
| CoreGraphics CFRetain
|__ CoreGraphics CGColorSpaceRetain
0x7fff2504971c:
| CoreGraphics cs_retain_count
|__ CoreFoundation _CFRetain
0x7fff2504e3d8:
| CoreGraphics CGColorSpaceRelease
|__ QuartzCore CABackingStorePrepareUpdates_(CABackingStore*, unsigned long, unsigned long, unsigned int, unsigned int, unsigned int, unsigned long long, CA::GenericContext*, UpdateState*)
0x7fff2505fdd1:
| CoreGraphics CGSNewEmptyRegion
|__ QuartzCore CABackingStorePrepareUpdates_(CABackingStore*, unsigned long, unsigned long, unsigned int, unsigned int, unsigned int, unsigned long long, CA::GenericContext*, UpdateState*)
...
```

```py
def cmd_cz(debugger, command, result, _dict):
    args = command.split(' ')
    if len(args) < 1:
        print('cov <module name>')
        return

    module_name = args[0]
    target = debugger.GetSelectedTarget()
    module = find_module_by_name(get_target(), module_name)

    # to keep track of the breakpoints set
    bpmap = {}
    print("[+] Creating breakpoints for all symbols in", module_name)
    for symbol in module:
        sym_name = symbol.GetName()
        if sym_name.startswith("os") or "pthread" in sym_name or "lock" in sym_name or "operator" in sym_name:
            continue
        address = symbol.GetStartAddress().GetLoadAddress(target)
        bp = target.BreakpointCreateByAddress(address)
        bpmap[address] = bp
    print("[+] Done creating breakpoints for all symbols in", module_name)

    visited = []
    while True:
        get_process().Continue()

        thread = get_thread()
        rip = int(str(get_frame().reg["rip"].value), 16)

        if rip in visited:
            continue

        if rip not in bpmap.keys():
            print("[+] Dead")   # crashed or something
            break
        # disable breakpoint after reaching it one time
        bpmap[rip].SetEnabled(False)

        print(hex(rip) + ":")
        for i in range(2):
            frame = thread.GetFrameAtIndex(i)
            symbol = frame.GetSymbol()
            module = frame.GetModule().GetFileSpec().GetFilename()

            print("|" + "__" * i, module, symbol.GetName())
```

In this example, I created a breakpoint using `target.BreakpointCreateByAddress(address)`, by using the `SBSymbol` methods to get a function's address in the process. I also used the lldbinit helper function `find_module_by_name` to get a `SBModule` object given a module name.

There were more unmentioned API calls used by this command, read the code to learn more if you are interested.

That's all. Hope you find this useful :D


**References:**
- [LLDB docs](https://lldb.llvm.org/python_reference/lldb-module.html)
- [lldbinit](https://github.com/daniellimws/lldbinit)
  - fork of [peter's](https://github.com/peternguyen93/lldbinit)
    - fork of [gdbinit's](https://github.com/gdbinit/lldbinit)
      - fork of [deroko's](https://github.com/deroko/lldbinit)