---
layout: post
title: INSOMNIHACK CTF Teaser 2016 Write Up - Smartcat1
author: xuehui
description: INSOMNIHACK CTF Teaser 2016 Write Up - Smartcat1
tags: [INSOMNIHACK,CTF]
excerpt: ""
comments: true
share: true
---

##### Goal:	To find the file which holds information on the flag.

##### Line of thought: Find a way to input multiple commands into the shell so that you can do other things like "ls" instead of only just pinging. 

------

#####<u>Tools used:</u>
- Google Chrome console
- ASCII table
- Some CMD knowledge 

### Preparation Stage
-----
I started this problem by typing some things into the textbox that was available on the website. Some simple choices I started with was "localhost" and "ccc" just to see what I could ping/what would happen. I also tried some other special characters to see if I could input special commands or anything.
I managed to conclude the following based on the error messages:

- These characters are blocked:  ```$;&|({`\t```
- The textbox acts like a bash/shell in linux, because of the ping –c 1 “xxxx” 
		
		
Next, in order to gain more information about how the form works, I inspected the page by opening up the console, and changing the form method from "POST" to "GET", so that I could see how the form values appear in the website link. 
		

With the help of some basic command line knowledge and an ASCII table, one knows that we can type multiple commands into the shell by using ";" and "&&", but we know that these 2 characters are blocked. Thus, the only option left is to use "\n", also known as the newline character. Which means, insert the command into a new line. Using the ASCII table, we find that the hexadecimal value of the newline is %0A. 
		

###Get the Flag Stage
------- 
After that, I did a http://smartcat.insomnihack.ch/cgi-bin/index.cgi?dest=localhost%0Als to list whatever was in the current directory. We see that there is a directory called "there", but we don't know how deep the flag is in "there" as there could be multiple subdirectories.
		

So, I did a a http://smartcat.insomnihack.ch/cgi-bin/index.cgi?dest=localhost%0Afind to list everything in the current directory (including sub-directories), and many subdirectories will show up, but only one of them is the flag. Although the deepest item does not have a file extension, there is no harm trying to cat the file to see if it contains the flag. 
		

Run something like this: [http://smartcat.insomnihack.ch/cgi-bin/index.cgi?dest=localhost%0Acat<./there/.../file](http://smartcat.insomnihack.ch/cgi-bin/index.cgi?dest=localhost%0Acat<./there/.../file)
		
just like how you can do something like ./a.out<input1.in 

		
and bingo! the flag will be displayed on the website:
		
<b>INS{warm_kitty_smelly_kitty_flush_flush_flush} </b>


