---
author: "Jh123x"
title: "[LakeCTF Finals 2022] Calc-You-Later"
date: 2022-11-07T00:00:00+08:00
description: "LakeCTF Finals 2022: Calc-You-Later (Web) writeup."
tags: ["writeups", "ctf", "web"]
ShowBreadcrumbs: False
---

## LakeCTF Finals

Last weekend was LakeCTF finals. This was my first time participating in a CTF finals overseas and had lots of fun!

This is a writeup for the Web: `Calc-You-Later` challenge which was solved together by [Devesh](https://linkedin.com/in/devesh-logendran) and I from NUS Greyhats.

This is an adaptation from the original post [here](http://jh123x.com/blog/2022/lakectf-finals-web-calc-you-later/)

## Challenge Description

```
Calc-You-Later, alligator.
Call /getflag and follow the instructions
http://chall.polygl0ts.ch:4600
```

It also provides a link to the source code of the challenge which is provided [here](https://github.com/Jh123x/LakeCTF-Finals/tree/main/Calc-You-Later "Calc-You-Later Download").

## Initial Thoughts

The zip file contains a source repository containing the source code of the challenge. The challenge is a simple web application that allows users to perform calculations.

The steps below outlined how we approached the challenge.

## Dockerfile

After that, we looked at the `dockerfile` of the challenge.

```dockerfile
FROM ruby:3.1.2

COPY src/ /app
COPY master.key /app/config/master.key
COPY flag secret getflag /
RUN chmod 4511 /getflag && chmod 400 /flag /secret
WORKDIR /app
ENV RAILS_ENV=production
RUN bin/bundle
RUN bin/rails db:prepare && bin/rake assets:precompile
RUN chmod -R 755 /app && chown -R root:root /app
RUN useradd rails && chown -R rails /app/tmp && chmod -R o+w /app/log && chown rails /app/db /app/db/production.sqlite3
USER rails

CMD ["bin/rails", "server", "-b", "0", "-e", "production"]
```

Let us walk through what the dockerfile does.

1. Uses Ruby 3.1.2 as the base image
2. Copies the source code into the container
3. Copies the `master.key` file into `/app/config/master.key`
4. Copies `flag`, `secret` and `getflag` into the root directory
5. Changes the permissions of `getflag` to `4511`.
   - Owner: Read, Execute, Group: Execute, Others: Execute, With recursion and setguid
   - This means that when others execute the code, they will have the temporarily uid of the owner.
6. Change the permissions of `flag` and `secret` to `400`
   - Owner: Read
7. Sets the working directory to `/app`
8. Sent environment to production
9. Ruby compile stuff
10. Add rails user and change permission to allow rails user to access various directories.
11. Run the rails server

Another point to note: the dockerfile indicates the presence of the following files which were not included in the zip:

1. `master.key`
2. `credentials.yml.enc`
3. `getflag`
4. `flag`
5. `secret`

## Directory Structure of the code

The directory looks something like this:

```bash
/
├── tmp
├── app
│   ├── tmp
│   ├── log
│   ├── db
│   ├── ... # Other files
│   └── config
│         ├── credentials.yml.enc
|         └── master.key
├── getflag
├── flag  
├── secret
└── ... # Other unrevelent files
```

## Setting up the environment

As there are many files which were not provided to us during the CTF challenge, we found outselves unable to run the dockerfile directly.

In order to test it in our local enironment, we have to either recreate the file or modify the dockerfile.

For most of the file we decided to use empty files to replace them. However, for the `credentials.yml.enc` file, we decided to not use it and remove the dockerfile line that uses it.

The revised dockerfile is as follows:

```dockerfile
FROM ruby:3.1.2

COPY src/ /app
# COPY master.key /app/config/master.key # Not used
COPY flag secret getflag /
RUN chmod 4511 /getflag && chmod 400 /flag /secret
WORKDIR /app
ENV RAILS_ENV=production
RUN bin/bundle

# Line added
RUN EDITOR=vim bin/rails credentials:edit

RUN bin/rails db:prepare && bin/rake assets:precompile
RUN chmod -R 755 /app && chown -R root:root /app
RUN useradd rails && chown -R rails /app/tmp && chmod -R o+w /app/log && chown rails /app/db /app/db/production.sqlite3
USER rails

CMD ["bin/rails", "server", "-b", "0", "-e", "production"]
```

With the dockerfile below, we can now build the dockerfile and run it locally.

## Looking at the source code

After making the dockerfile work, we looked at the source code. The first section which we inspected was the application controllers.

There were 2 main controllers which were used by the application.

### User controller

```ruby
class UserController < ApplicationController
  def index
  end

  def login
    user = User.find_by(username: params[:username])
    if user == nil then
      user = User.new(username: params[:username], password: params[:password])
      user.save
      puts "Create user #{user.username}"
    end
    if user.password == params[:password] then
      puts "Logged in user #{user.username}"
      session[:user] = user.username
      redirect_to controller: :home, action: :index
    else
      puts "Failed login for #{user.username}"
      render :index
    end
  end
end
```

This controller does the following:

1. Takes in the username and password from the user
2. If the user does not exist, create a new user
3. If the password is equal to the one in the database, allow the user to login
4. Otherwise, throw a login error.

### Home controller

```ruby
class HomeController < ApplicationController
  def index
    redirect_to controller: :user, action: :index unless session[:user]
    @user = User.find_by(username: session[:user])
    @results = Result.where(user: @user).order(created_at: :desc).limit(10)
  end

  def post
    redirect_to controller: :user, action: :index unless session[:user]
    @user = User.find_by(username: session[:user])
    CalcJob.set(wait: 1.minutes).perform_later(params[:program], @user)
    redirect_to action: :index
  end
end
```

The code here is more interesting. The top function shows the homepage of the user and gets the last 10 results of the user. The bottom function takes in the user input and passes it to the `CalcJob` function.

However, **the program waits for 1 minute before the CalcJob function is actually called.**

In order to make debugging faster for us, we removed the 1 minute timer in the source code.

After going through all of these, the next logical step was to look at the `CalcJob` function.

### CalcJob

Under the Jobs section, we manage to found the CalcJob source code. It performs only 1 job, run the program in `SafeRuby` and save the result to the database.

```ruby
require "safe_ruby"
class CalcJob < ApplicationJob
  queue_as :default

  def perform(program, user)
    res = SafeRuby.eval(program)
    print("Running program", program)
    Result.new(result: res.to_s, user: user).save
  end
end
```

The `SafeRuby` class is a class which is used to run the program in a sandboxed environment. It is a class which is provided by the `safe_ruby` gem. We then went to the [github page](https://github.com/ukutaht/safe_ruby "Github page of SafeRuby") of the gem to see how it works.

From the above, we can see that we are able to read all the files except for this which have permission bits set to `400`.

```bash
/
├── tmp
├── app
│   ├── tmp
│   ├── log
│   ├── db
│   ├── ... # Other files
│   └── config
│         ├── credentials.yml.enc
|         └── master.key
├── getflag # (Executable by us)
├── flag    # (Not readable by us)
├── secret    # (Not readable by us)
└── ... # Other unrevelent files
```

### SafeRuby

We spent a while looking at how the SafeRuby source code works. It turns out that it writes a ruby file to another location before running it.

Before the application actually runs the program, there are some built in functions which the code removes before it actually executes the user defined code.

To find out how it actually works, we will have to take a deeper dive at what program it produces when we key in a code of our own.

This is the code generated by SafeRuby. It is a ruby file which is written to the `/tmp` directory. The code is then executed by the `ruby` command.

After the execution of the code, ruby deletes the tmp file. In order for use to actually get the file, we will have to hit the timeout of `5s` to stop the process before the file is actually deleted.

```ruby
def keep_singleton_methods(klass, singleton_methods)
  klass = Object.const_get(klass)
  singleton_methods = singleton_methods.map(&:to_sym)
  undef_methods = (klass.singleton_methods - singleton_methods)

  undef_methods.each do |method|
    klass.singleton_class.send(:undef_method, method)
  end

end

def keep_methods(klass, methods)
  klass = Object.const_get(klass)
  methods = methods.map(&:to_sym)
  undef_methods = (klass.methods(false) - methods)
  undef_methods.each do |method|
    klass.send(:undef_method, method)
  end
end

def clean_constants
  (Object.constants - [:Object, :Module, :Class, :BasicObject, :Kernel, :NilClass, :NIL, :Data, :TrueClass, :TRUE, :FalseClass, :FALSE, :Encoding, :Comparable, :Enumerable, :String, :Symbol, :Exception, :SystemExit, :SignalException, :Interrupt, :StandardError, :TypeError, :ArgumentError, :IndexError, :KeyError, :RangeError, :ScriptError, :SyntaxError, :LoadError, :NotImplementedError, :NameError, :NoMethodError, :RuntimeError, :SecurityError, :NoMemoryError, :EncodingError, :SystemCallError, :Errno, :ZeroDivisionError, :FloatDomainError, :Numeric, :Integer, :Fixnum, :Float, :Bignum, :Array, :Hash, :Struct, :RegexpError, :Regexp, :MatchData, :Marshal, :Range, :IOError, :EOFError, :IO, :STDIN, :STDOUT, :STDERR, :Time, :Random, :Signal, :Proc, :LocalJumpError, :SystemStackError, :Method, :UnboundMethod, :Binding, :Math, :Enumerator, :StopIteration, :RubyVM, :Thread, :TOPLEVEL_BINDING, :ThreadGroup, :Mutex, :ThreadError, :Fiber, :FiberError, :Rational, :Complex, :RUBY_VERSION, :RUBY_RELEASE_DATE, :RUBY_PLATFORM, :RUBY_PATCHLEVEL, :RUBY_REVISION, :RUBY_DESCRIPTION, :RUBY_COPYRIGHT, :RUBY_ENGINE, :TracePoint, :ARGV, :Gem, :RbConfig, :Config, :CROSS_COMPILING, :Date, :ConditionVariable, :Queue, :SizedQueue, :MonitorMixin, :Monitor, :Exception2MessageMapper, :IRB, :RubyToken, :RubyLex, :Readline, :RUBYGEMS_ACTIVATION_MONITOR]).each do |const|
    Object.send(:remove_const, const) if defined?(const)
  end
end

keep_singleton_methods(:Kernel, ["Array", "binding", "block_given?", "catch", "chomp", "chomp!", "chop", "chop!", "eval", "fail", "Float", "format", "global_variables", "gsub", "gsub!", "Integer", "iterator?", "lambda", "local_variables", "loop", "method_missing", "proc", "raise", "scan", "split", "sprintf", "String", "sub", "sub!", "throw"])
keep_singleton_methods(:Symbol, ["all_symbols"])
keep_singleton_methods(:String, ["new"])
keep_singleton_methods(:IO, ["new", "foreach", "open"])

keep_methods(:Kernel, ["==", "ray", "nding", "ock_given?", "tch", "omp", "omp!", "op", "op!", "ass", "clone", "dup", "eql?", "equal?", "eval", "fail", "Float", "format", "freeze", "frozen?", "global_variables", "gsub", "gsub!", "hash", "id", "initialize_copy", "inspect", "instance_eval", "instance_of?", "instance_variables", "instance_variable_get", "instance_variable_set", "instance_variable_defined?", "Integer", "is_a?", "iterator?", "kind_of?", "lambda", "local_variables", "loop", "methods", "method_missing", "nil?", "private_methods", "print", "proc", "protected_methods", "public_methods", "raise", "remove_instance_variable", "respond_to?", "respond_to_missing?", "scan", "send", "singleton_methods", "singleton_method_added", "singleton_method_removed", "singleton_method_undefined", "split", "sprintf", "String", "sub", "sub!", "taint", "tainted?", "throw", "to_a", "to_s", "type", "untaint", "__send__"])
keep_methods(:NilClass, ["&", "inspect", "nil?", "to_a", "to_f", "to_i", "to_s", "^", "|"])
keep_methods(:TrueClass, ["&", "to_s", "^", "|"])
keep_methods(:FalseClass, ["&", "to_s", "^", "|"])
keep_methods(:Enumerable, ["all?", "any?", "collect", "detect", "each_with_index", "entries", "find", "find_all", "grep", "include?", "inject", "map", "max", "member?", "min", "partition", "reject", "select", "sort", "sort_by", "to_a", "zip"])
keep_methods(:String, ["%", "*", "+", "<<", "<=>", "==", "=~", "capitalize", "capitalize!", "casecmp", "center", "chomp", "chomp!", "chop", "chop!", "concat", "count", "crypt", "delete", "delete!", "downcase", "downcase!", "dump", "each", "each_byte", "each_line", "empty?", "eql?", "gsub", "gsub!", "hash", "hex", "include?", "index", "initialize", "initialize_copy", "insert", "inspect", "intern", "length", "ljust", "lines", "lstrip", "lstrip!", "match", "next", "next!", "oct", "replace", "reverse", "reverse!", "rindex", "rjust", "rstrip", "rstrip!", "scan", "size", "slice", "slice!", "split", "squeeze", "squeeze!", "strip", "strip!", "start_with?", "sub", "sub!", "succ", "succ!", "sum", "swapcase", "swapcase!", "to_f", "to_i", "to_s", "to_str", "to_sym", "tr", "tr!", "tr_s", "tr_s!", "upcase", "upcase!", "upto", "[]", "[]="])
Kernel.class_eval do
 def `(*args)
   raise NoMethodError, "` is unavailable"
 end

 def system(*args)
   raise NoMethodError, "system is unavailable"
 end
end

clean_constants

      result = eval(%q(1 + 1;sleep(5);))
      print Marshal.dump(result)
```

As seen above, there is a long list of functions which are blocked by the SafeRuby class. This is to prevent the user from executing malicious code.

## Finding vulnerable functions

Now that we know what functions are available to us, we can start to look for functions which we can use to exploit the application.

While we were doing the sections above, we stumbled across [a website](https://petircysec.com/slashroot-3-0-rubycalc_v1_0/ "SlashCTF Challenge") which contained a similar challenge.

In the case above, the user made use of the unblacklisted `open` function to read the contents of a file. Thus, we decided to do the same thing to read the files which were not provided to us.

Function that we used.

```ruby
open('filename', &:read)
```

As we did not have permission to read flag.txt, we will have to find a way to execute `getflag` in order to get the flag.

## Finding a way to execute code

After digging through a lot of documentation, we stumbled upon [Kernel Documentation](https://ruby-doc.org/core-3.1.2/Kernel.html) from ruby and found out that the spawn method was not blacklisted.

We have now found a way to execute code on the challenge server. With that knowledge in mind, we can now execute `getflag` to get the flag on the server.

We can make use of `spawn` and the `>` operator to redirect the output of `getflag` to a file.

Subsequently, we can spawn another process to read the file to see what is the output of `getflag`.

Code snippet

```ruby
# First payload to write to the file
spawn('/getflag > /tmp/lol');

# Second payload to read the file
open('/tmp/lol', &:read);
```

## Actually running `getflag`

At first, we thought that `getflag` would just be the completion of the challenge. However, there was a further step we have to solve before getting the flag.

When we first ran `getflag`, we got the following output:

```
Please provide the secret from `/app/config/credentials.yml.enc` as argv[1]
```

The `getflag` binary actually require us to get the `secret` from the `credentials.yml.enc` file.

So we went to read the file with

```ruby
open('/app/config/credentials.yml.enc', &:read)
```

## Figuring out what is `/app/config/credentials.yml.enc`

As we have no idea how to decrypt `credentials.yml.enc`, we decided to do a quick google search to figure that out.

We stumbled across a [github repository](https://github.com/mbadanoiu/Credentials.yml.enc_Decryptor "Credentials.yml.enc Decryptor") which contains a script to decrypt the file. However, in order to decrypt the file, we have to obtain `master.key` which is the next file we read from the server.

After running the command, we managed to get the secret from the `credentials.yml.enc` file.

```
yay_you_decoded_me_now_go_get_your_flag_boiii
```

**Note:** For those facing an issue in python3, there are you will have to update the `unmasterify` function to the one below.

```python
def unmasterify(master_key):
	return bytes.fromhex(master_key)
```

## Getting the flag

After getting the secret, we can now run `getflag` again to get the flag.

Flag: `EPFL{https://youtu.be/ya2Sx8xmMpc#Because_why_not...}`
