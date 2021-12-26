---
author: "Chan Jian Hao"
title: "HTXIC CTF Reversing 101 Writeup"
date: "2021-12-27"
description: "Writeup/Walkthrough to reversing a unwinnable tic-tac-toe CTF game"
tags: ["writeups", "ctf", "reverse engineer"]
ShowBreadcrumbs: False
---

# Overview

The HTX Investigators’ Challenge (HTXIC) 2021 was a CTF competition with about ~128 teams participating, it was held on 20 Dec 2021. This post will document a writeup on the challenge `Reversing 101` as I thought it is quite a fun to reverse a tic tac toe game and find flag.

{{< figure src="/images/HTXIC-Reversing101/photo_2021-12-20_08-11-23.jpg" >}}

PS: The CTF came in a mixed reality/game world concept where instead of the usual web portal where we submit our flags, it is done in a Unity game mirroring the HTX Office in real life. We get to see our teammates in game too in various avatars and have to uncover/hunt for 'quests' in game to unlock the CTF challenges. Other than the lack of features in-game like the ability to chat, this CTF was quite a novel one replacing the face to face physical competitions in this COVID-19 pandemic. 


# Understanding the binary

Since the challenge title already hints that this is a reversing challenge, we can first analyse the binary to know what we are dealing with.

Uploading the file onto VirusTotal is a easy way to figure it, so upon upload we get the following:

{{< figure src="/images/HTXIC-Reversing101/Image1-VT.png" >}}

Access the full VirusTotal Report [here](https://www.virustotal.com/gui/file/164153fe29038e87357c08759829b4c76bb857b715b59c2f7e2474809fc1d19b/details)

Indicating that this binary is a PE32 executable for MS Windows (GUI) Intel 80386 32-bit Mono/.Net assembly.

{{< figure src="/images/HTXIC-Reversing101/214854.png" >}}

We can verify this using local tools as well, for example on Detect It Easy, identifying it to be a .NET(v4.0.30319) binary. This would mean that we can test this binary out on a Windows VM. Interestingly, Confuser(1.X) was also identified, this will be useful later.


# Testing the binary

Before going into reversing the program, let's first understand what it does. 

{{< figure src="/images/HTXIC-Reversing101/215438.png" >}}

Launching the program, we expectedly get a GUI program showing a Tic Tac Toe game. The symbols and font of the game looks suspicious, and in the background a familiar soundtrack from the popular Squid Game Netflix drama is playing, perhaps a sign that this game is rigged and we have no way to legitimately win 😥😥. 

{{< figure src="/images/HTXIC-Reversing101/215948.png" >}}

After some testing of the game by manually playing the tic tac toe against the 'AI', and manipulating the game with some basic [Cheat Engine](https://github.com/cheat-engine/cheat-engine), seems like high score is not something that would reveal the challenge flag. 

Besides score, the game is pretty basic. Just click anywhere on the 3x3 game tile to play the game, after you win or lose, click next round. Alternatively, the score resets when you click on the 'Reset' button. 

Looks like the flag won't come easily, and does require some 101 reversing efforts.


# Reversing the binary

Since Detect It Easy has identified that this is a .NET application, we can make use of our handy [dnSpy](https://github.com/dnSpy/dnSpy) to deal with it. This tool is a useful debugger and .NET assembly editor and it does come with the feature to decompile .NET. This is great news, as we do not have to use tools like Ghidra or IDA Pro. 

{{< figure src="/images/HTXIC-Reversing101/220738.png" >}}

Upon opening it on dnSpy, we can see that the code is successfully reversed. Unfortunately, we see some form of obfuscation, where things like the method names are not in plaintext. 

{{< figure src="/images/HTXIC-Reversing101/221725.png" >}}

This is due to the use of Confuser by the authors, which we have identified earlier. This technique is also commonly used by malware authors if they want to hinder analysis efforts. For this challenge, more specifically, it is `ConfuserEx v1.0.0` which was used to obfuscate the binary. To solve this, we could use [de4dot](https://github.com/de4dot/de4dot) to clean up the binary. 

```
PS C:\Users\Alice\Desktop > de4dot-x64.exe .\TicTacToe.exe

de4dot v3.1.41592.3405 Copyright (C) 2011-2015 de4dot@gmail.com
Latest version and source code: https://github.com/0xd4d/de4dot

Detected Unknown Obfuscator (C:\Users\Alice\Desktop\TicTacToe.exe)
Cleaning C:\Users\Alice\Desktop\TicTacToe.exe
Renaming all obfuscated symbols
Saving C:\Users\Alice\Desktop\TicTacToe-cleaned.exe
```

{{< figure src="/images/HTXIC-Reversing101/222222.png" >}}

Finally, the code is cleaned and readable. The code is quite long, with about 1000++ lines in total. So I will just summarize what are the main points to solving this challenge.

Firstly, at the entry point, it runs Form1 which is the main Tic Tac Toe game interface we saw earlier.

```csharp
private static void Main()
	{
		Application.EnableVisualStyles();
		Application.SetCompatibleTextRenderingDefault(false);
		Application.Run(new Form1());
	}
```

Tracing the click events, we see that for each tile of the tic tac toe clicked by the user, the code is as follows:

```csharp
	private void button1_Click(object sender, EventArgs e)
	{
		if (!this.bool_0)
		{
			if (this.list_0.Contains('1'))
			{
				this.button1.ForeColor = Color.Lime;
				this.button1.Text = "O";
				this.list_0.Remove('1');
				this.method_8();
			}
		}
		else
		{
			this.method_10('1');
			this.method_11();
		}
	}
```

{{< figure src="/images/HTXIC-Reversing101/223058.png" >}}

In method_8, it does a series of if else conditions to check if the player has won the game, lost it, or if it ended in a draw, and displays the message box accordingly. 

This can be confirmed by base64 decoding:
```
echo WW91IHdpbiEgUHJlc3MgTmV4dCBSb3VuZCB0byBjb250aW51ZS4= | base64 --decode

You win! Press Next Round to continue.

echo WW91IExvc2UhIFByZXNzIE5leHQgUm91bmQgdG8gY29udGludWUu | base64 --decode

You Lose! Press Next Round to continue.

echo RHJhdyEgUHJlc3MgTmV4dCBSb3VuZCB0byBjb250aW51ZS4= | base64 --decode

Draw! Press Next Round to continue.
```

However, there is an additional if statement after the checks for win/lose/draw condition, checking if 2 integers are 3 and 2 respectively.

```
if (this.int_0 == 3 && this.int_1 == 2)
		{
			this.method_9();
		}
```

Turns out that this is referring to the player score. So let's go back to the binary and give it a play.

{{< figure src="/images/HTXIC-Reversing101/224141.png" >}}

To speed things up a little, as the AI was too dumb to win me, I edited the memory to give it the desired scores of 3 and 2 for myself and the AI. This could be played manually too, though time consuming.

Upon clicking any tiles with the score, the condition is met and a message box pops up telling us "You have accessed the hidden locker, can you unlock it?". How fun, we have unlocked a secret stage to this game.

Clearly, this is also hinting to us that we are closer to the flag now. 

# Hidden Locker (Secret Stage?!) in the bianry

{{< figure src="/images/HTXIC-Reversing101/224607.png" >}}

Now, the GUI of the .NET binary has changed. This is no longer a tic tac toe game but we have a PIN pad. 

The code does something along the lines of:
- Checking the length of the PIN to be 9
- Calling method 12, which checks our PIN

```
if (this.string_0.Length == 9)
		{
			this.method_12();
		}
```


{{< figure src="/images/HTXIC-Reversing101/224935.png" >}}

From here, we see that it uses the PIN along with strings in the method `Y0uSh0uldPr3ssth`, `3butt0ns` and `TtH/04xZb79By/VnbPZlBgO/D96vRmqPk0QT50gbdi8=` for AesCrypto. Now this is the crypto part of the reversing challenge. 

But firstly, we have to ensure we pass the lengthy if condition for our PIN to even trigger the decryption routine. As it would be used for decryption, there is no point patching this binary to skip the statement as it would not give us the flag even if we manipulated the if condition.

```
if (num % num9 == 0 && num9 * 3 == num2 && num2 * 3 == num4 && num5 % num2 == 2 && num6 * 4 == num5 && num2 % num4 == num3 && num2 - num6 == num && num7 % num3 == num && num7 / 2 == num6 && num8 % num2 == num9 && num8 % num7 == num2)
```

Since this is something very painful to be done manually, I have decided to use some python scripting help along with [z3](https://github.com/Z3Prover/z3) black magic. 

To install, we can simply use pip.
```cmd
 pip install z3-solver
```


Z3 is basically a theorem prover from Microsoft Research, this would help us to solve the equations given the amount of constraints imposed by the game. Please pardon me for my amateur z3 code, I am still learning it.


```python
from z3 import *

s = Solver()
pin_code = IntVector("x", 9)

# Add constraints to z3

s.add(pin_code[0] % pin_code[8] == 0)
s.add(pin_code[8] * 3 == pin_code[1])
s.add(pin_code[1] * 3 == pin_code[3])
s.add(pin_code[4] % pin_code[1] == 2)
s.add(pin_code[5] * 4 == pin_code[4])
s.add(pin_code[1] % pin_code[3] == pin_code[2])
s.add(pin_code[1] - pin_code[5] == pin_code[0])
s.add(pin_code[6] % pin_code[2] == pin_code[0])
s.add(pin_code[6] / 2 == pin_code[5])
s.add(pin_code[7] % pin_code[1] == pin_code[8])
s.add(pin_code[7] % pin_code[6] == pin_code[1])

# Solve

res = s.check()
if res == sat:
    print(s.model())
else:
    print("Response: %s" % res)

```


Running the z3 solver would provide us the PIN, after doing some rearrangement:
 
```
 x__3 = 9
 x__4 = 8
 x__1 = 3
 x__8 = 1
 x__5 = 2
 x__6 = 4
 x__0 = 1
 x__7 = 7
 x__2 = 3
 
 PIN = 133982471
```


{{< figure src="/images/HTXIC-Reversing101/225839.png" >}}

Finally, with the PIN cracked, we can just enter it in the game using the GUI and a message box will appear giving us the flag!

`You have obtained the flag! HTX{R3v3rsingCSh4rplsE4sy}`

Ggwp.



# References

https://ericpony.github.io/z3py-tutorial/guide-examples.htm

https://github.com/ViRb3/z3-python-ctf

https://rolandsako.wordpress.com/2016/02/17/playing-with-z3-hacking-the-serial-check/

https://wiki.bi0s.in/reversing/analysis/dynamic/linux/z3/

https://labs.f-secure.com/assets/BlogFiles/mwri-hacklu-2018-samdb-z3-final.pdf

https://github.com/dnSpy/dnSpy

https://github.com/de4dot/de4dot
