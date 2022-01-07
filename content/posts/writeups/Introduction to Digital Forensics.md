
---
author: "erythr0"
title: "Introduction to Digital Forensics"
date: "2022-01-07"
description: "A beginner's guide to Digital Forensics"
tags: ["writeups"]
ShowBreadcrumbs: False
---


# An introduction to Digital Forensics

Welcome to a beginner's guide to Digital Forensics. This writeup explains how forensics is applied in the real world, and common techniques/challenges used in [CTFs](https://ctf101.org/). 

If you are already well versed with digital forensics and would like to learn about digital forensics in CTFs, you may [skip to here](#forensics-in-ctfs).

The term "forensics" originally means "of or before the forum".
The modern meaning of "forensics" is a form of legal evidence to be presented.

# Digital Forensics

There are multiple aspects of digital forensics. They include but are not limited to:
- File System Forensics
- Memory Forensics
- Network Forensics
- Database Forensics
- Application Forensics	
- E-mail Forensics

In this writeup, we will be focusing on file system forensics, memory forensics, and network forensics.

## File System Forensics

Files or folders can be hidden by:
- having their names start with `.` (in Linux)
- marking them as hidden (in Windows)
- deleting them

Deleted files can potentially be recovered through data carving using specific tools. Data carving is the process of reassembling computer files from fragments in the absence of filesystem metadata.

When a file is deleted, the entry in the file system metadata is removed. However, the actual data still remains on the disk. The data is only lost after is is overwritten by new data. This is why in digital forensics, it is important to not write new data into the medium that you are carrying out forensics on. 

Let me explain this in simpler terms.

Imagine you have a folder containing many sheets of papers. The first page of the folder is a contents page. In real life, when you want to remove a piece of paper from the file permanently, you will simply do so. However, that is not the case for computers. In computers, what happens is that the entry in the contents page gets erased, while the piece of paper inside the file remains untouched. When you need to write new information into the folder, the pages with the unwanted content gets overwritten with new information, while what remains of the old, unwanted information gets ignored.


## Memory Forensics

Hard disks contain a myriad of data. They not only contain data of your files and folders, but also store much more information that most people aren't aware about. These essential groups of data contain metadata and other bits of information that makes a hard drive behave correctly. Chunks of space in hard drives are reserved for metadata. 

However, not all of the space in those chunks are actually utilized. This allows for data to be written and hidden in these empty spaces, and will not affect the typical functionality of the hard drive in any way. Examples of parts of the hard drive where data can be hidden in are:

1. Host Protected Area & Device Configuration Overlay
2. Unused space in the Master Boot Record
3. Volume Slack
4. Partition Slack
5. Boot sector in Non-bootable partition
6. Unallocated space in a partition
7. Good blocks marked "bad"
8. Disk Slack
9. Unused space in superblock
10. Unused space in block groups 
11. Directory entries

Data hidden in these areas of a hard disk can be found through the process of data carving.


# Network Forensics
Network traffic is extremely volatile. This is why network forensics should be a proactive investigation.

### How to capture network traffic?

To capture network traffic, run a network monitoring tool such as [Wireshark](https://www.wireshark.org/).

### Where to capture network traffic?

Network traffic should be captured at an endpoint, such as a proxy server, or at a forensic PC connected to the mirror port of  an internet router.

At these locations, investigators can collect unicast traffic sent from the host in question.

## Computer networks have layers
In computer networking, there are several layers. Namely, the application, transport, network, and data-link layers. Each layer employs different protocols to ensure that data moves from one place to another correctly. This also means that there are different types of evidence we can collect from each layer.

### Application Layer

- DNS, SMTP, HTTPS and HTTP protocols are used in this layer.
	- DNS translates a domain name into the correct IP address.
	- SMTP is used to send and receive mail.
	- HTTPS is the secure version of HTTP, and is used to fetch HTML files to load web pages. 

HTTP is not secure because the data is sent in plain text. This means that if a malicious actor gained unlawful access into a network (or if they are in the vicinity of the victim), and if a user enters their login details to gain access to their own account on a certain website, the malicious actor will be able to acquire them by sniffing the packets on the network sent by the unsuspecting user. HTTPS fixes that problem by encrypting data being received and sent by a host, and is still in use to this day since its introduction in 1994.

### Transport Layer
The transport layer optionally ensures the reliability of communications. TCP and UDP are used here.
- TCP is used in connections where reliability is of paramount importance. For example, in sending texts, streaming videos, or sending files.
- UDP is used in connections where speed takes precedence over reliability. For example, in voice/video calls, or when playing online games.

### Network Layer
The network layer is in charge of routing packets across networks.

The IP protocol delivers packets from the source host to destination host based on the IP addresses present in packet headers.

### Data-link layer
The data-link layer is responsible for handling communications on the physical network components.

With the right expertise and tools, it is possible to eavesdrop on wired communications that go through ethernet cables.

## Network Forensics as a whole

At the beginning of network forensics, investigators begin with very limited information such as an IP address, port number, and a protocol. 

However, this is enough to search for more sources of data for more useful information. Most of the time, the more useful evidence is found in the application layer. 

Despite this, investigators might still need additional IP addresses to identify other hosts involved in the malicious activity. Certain activities such as DDoS attacks do not have relevant application-level data. 

Therefore, network forensics provides important support to the overall analysis of activities happening in the application layer.

# Forensics in CTFs

In CTFs, forensics is a very popular category. It is also important to note that while some challenges may be marked as "forensics", they **may also employ steganography and/or cryptography** to make them even more challenging. 

Steganography involves hiding data, while cryptography makes it difficult to understand the data. More often than not, you might find yourself going deep into rabbit holes that lead to absolutely nowhere. It may be frustrating, but it'll feel very rewarding once you get the flag. 

Here are some tips you can use to get started with common/easy forensics challenges.


## Image Forensics

### Hidden in plain sight

![Before fill and after fill](https://i.imgur.com/R4CFtbO.png)
>Sometimes, information can be hidden in plain sight, just like in this example

On the left is an image downloaded from a challenge. On the right is the same image but filled with white, which reveals a hidden URL in a slightly different tone of red.

 ### Metadata

Instead of data being hidden inside the image, they might be hidden in the metadata. By viewing the properties of the file or by using an online tool such as [Jeffrey's Image Metadata Viewer](http://exif.regex.info/exif.cgi), we will be able to see these information.

 
### Hidden strings
![Flag hidden as bytes](https://i.imgur.com/4Cvl7qR.png)
> Potential flag hidden in the image file, when viewed using [HxD](https://mh-nexus.de/en/hxd/), a hex editing software

When viewed in a text or hex editor, images may contain strings like these. These strings, when placed in certain parts of the file, will not affect the image in any way. This means that the image can still be opened and viewed normally using an image viewer. 

In Linux systems, there is a command called [strings](https://linux.die.net/man/1/strings). You can also run this command with the given file to see if there are any readable secrets.
 

### Hidden Files
![binwalk example](https://i.imgur.com/b0gNVA5.png)
> Hidden files found and extracted from a file using binwalk

Similar to hiding a string, files can also be hidden in images. They are typically concatenated after the bytes of the image. They can be found and extracted using tools such as [binwalk](https://github.com/ReFirmLabs/binwalk).

### Least Significant Bit (LSB)
As you know, files are made up of bytes. 1 byte is equal to 8 bits. If the least significant bit of a byte is changed, it will not change the overall value by too much. 

Using this idea, we can potentially hide messages in binary in images by altering the LSB of all the bytes. This will not make any significant change to the image, and will practically go unnoticed by humans.  

To extract the hidden information, simply go through all the bytes, take the LSB, and append it to the string of extracted LSBs. Then, convert the final binary string into a readable message or even a file.

## Audio Forensics

### Binary or Morse Code

A higher amplitude in a soundwave may denote a 1, while a lower amplitude may denote a 0. This is a simple way to hide messages encoded in binary or Morse code in an audio file. 

For challenges like this, a visual aid might be useful. I recommend opening the file in [Audacity](https://www.audacityteam.org/) first before conducting further analysis. 

### Spectrogram

Even though it is an audio file that you're dealing with, the challenge author might want to hide visual  information in the sound. Not in the form of waves, but through spectrograms. 

![QR Code hidden in spectrogram](https://i.imgur.com/8Tt9w6J.png)
> QR Code hidden in spectrogram

One way to view spectrograms in audio files is through this online tool called [Spectrum Analyzer](https://academo.org/demos/spectrum-analyzer/). 

## Network Forensics

### Hidden in plain sight
![Potential flag found](https://i.imgur.com/HpEukMi.png)
> HTTP sends unencrypted packets from one endpoint to another, making it incredibly unsecure 

Given a packet capture (pcap) file, you can find out what has been done, and what has been communicated over two hosts.

Firstly, open the pcap file in [Wireshark](https://www.wireshark.org/). For simpler challenges, the flags can be present in plain text— you'll just need to know what to look for. 

Unsecured protocols such as HTTP should first be used as filters to get the low-lying fruit. 

### Follow the TCP Stream
 
 On a given pcap file, there might be a need to extract messages or files that have been sent from one host to another. This can be done by following a TCP stream, then saving the contents of the stream. 

If there are multiple types of files in the packet capture, you may want to [check out this guide](https://unit42.paloaltonetworks.com/using-wireshark-exporting-objects-from-a-pcap/) on how to export objects from different types of traffic in Wireshark.

## Unknown file Forensics

### Identify file signature
What happens when you are given a file with an unknown extension, or even one that doesn't exist? 

Luckily, files always have two attributes we can use to identify them. The first way is through the extension (png, jpg, mp4, wav, pdf, etc.). The second way is through file signatures. 

A file signature is a unique group of bytes located at the beginning of a file. File signatures are used to identify or verify the content of a file. 

[Here is a list of file signatures](https://en.wikipedia.org/wiki/List_of_file_signatures) you can use to figure out what type of file you are dealing with.

Alternatively, you may also run [binwalk](https://github.com/ReFirmLabs/binwalk) on the unknown file to identify it automatically. 

### Broken file signatures

Uh oh, you realised that the extension of the given file is unknown, and the signature does not exist in the given list. Now what?

At this point, you may check if there are any similarities between the broken signature you were given, and legitimate signatures. Chances are, you might just need to fix a few bytes here and there before the file becomes readable.

Conversely, the file signature might be legitimate, but the file extension isn't. In such cases, simply change the extension to the one that corresponds to the file signature, and you'll be able to open the file.

## OS Forensics

### Event Logs
![A security log entry in event viewer](https://i.imgur.com/pv0Xrwe.png)
> A security log entry depicting a logon event in Event Viewer

Login time, number of failed password attempts, last logon attempt, and other security & system related logs can be found in the Event Viewer of Windows machines. They may provide useful information on what last went down in a computer system.

### Browser Artifacts
Browser history, passwords, cookies, temporary internet files, and bookmarks might prove to be useful in an investigation. Be sure to check these if you are given a snapshot of an operating system and need to look for clues.

### User Directories
Operating systems may contain different profiles. If what you are looking for is not present in the current user's directory, chances are that you might be looking in the wrong place. 

Try going to other users' home directories to see if there are any files of interest.

### Hidden Directories/Files
![A directory with the hidden attribute](https://i.imgur.com/7XQPYtJ.png)
> A directory in windows with the "Hidden" attribute

Still can't find what you are looking for? That might be a sign that some data cannot be seen through normal means. 

In Windows' File Explorer, check that `View -> Hidden Items` is enabled. This allows you to see hidden files and folders.

In Linux machines, files and folders may be renamed to begin with `.`, which will be ignored by the `ls` command. To show these hidden items, use `ls -a` instead.

# Conclusion
There are many more aspects of digital forensics that were not covered in this writeup, as it was meant to be a brief introduction for beginners. I personally recommend trying the challenges over at [ctflearn.com](ctflearn.com) for beginner-level ones. I hope you've enjoyed reading and have hopefully learnt something new! :) 

# References
**File system forensics:**
- https://en.wikipedia.org/wiki/File_carving

**Memory Forensics:**
- http://www.berghel.net/publications/data_hiding/data_hiding

**Network Forensics:**
-  https://en.wikipedia.org/wiki/Network_forensics
- http://testphp.vulnweb.com/login.php



# Forensics Tools/Guides/References for CTFs
**Image Forensics:** 
- https://ctflearn.com/challenge/934
- https://ctflearn.com/challenge/108
- http://exif.regex.info/exif.cgi
- https://stylesuxx.github.io/steganography/
- https://www.file-recovery.com/jpg-signature-format.htm
- https://ctf101.org/forensics/what-is-stegonagraphy/

			
**Unknown File Forensics:**
- https://www.easytechjunkie.com/what-is-a-file-signature.htm
- https://www.kali.org/tools/binwalk/
	
