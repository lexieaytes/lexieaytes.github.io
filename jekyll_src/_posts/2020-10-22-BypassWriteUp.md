---
title: HTB Write Up - Bypass
date: 2020-10-22 12:00:00 -0500
categories: [HTB, Reversing]
tags: [htb, hack the box, reversing, bypass, windows 10, dnspy]     # TAG names should always be lowercase
mermaid: true
toc: false
---
Technologies: Windows 10, Remnux, VirtualBox, dnSpy

We're given an executable `Bypass.exe` to analyse. I always like to start by running the `file` command to see what we're dealing with:

```console
$ file Bypass.exe
Bypass.exe: PE32 executable (console) Intel 80386 Mono/.Net assembly, for MS Windows
```

This tells us that we're working with a .NET application, and that means we can use a nifty tool called dnSpy to disassemble and debug it.

After opening `Bypass.exe` in dnSpy, it shows up in the Assembly Explorer as “HTBChallenge". Now it's time to start debugging! 

Let's begin by clicking the green start button at the top.

![image](/assets/img/BypassWriteUp/imageOne.png)

We want to find the file that's the entry point of the program, so let's change the 'Break at' default to 'Entry Point'. Click OK. A console running the executable should pop up, and dnSpy should show the entry point in class 0.

Click 'continue' a couple more times, and the executable asks for a username and password. Why not try ‘username’ and ‘password’?

Unfortunately, the program responds with `wrong error`. Rats.

Let's add a breakpoint to that first *if statement* to see what's going on. 

![image](/assets/img/BypassWriteUp/imageTwo.png)

Restart the program in dnSpy by clicking the round arrow at the top. Type in "username" and "password" again, and then the program should hit our breakpoint. Now we can see two variables **flag** and **flag2** listed under *locals*. Perhaps we should try setting these values to `True`...

![image](/assets/img/BypassWriteUp/imageThree.png)

It works! Now the program prompts us for a 'secret Key' that we don't know. Foiled again.

But wait! If we scroll down in the code, we find another *if statement*. Let's put another breakpoint there, and then for the 'secret Key', type in a random string. I typed 'key'. Hit enter. 

Now in dnSpy, we see:

![image](/assets/img/BypassWriteUp/imageFour.png)

A weird string. Maybe this is the HTB flag? 

Nope, it must be something else. Darn.

Maybe we need to change the flag to `True` again. Let's try that and restart the program in dnSpy. 

Once we hit our second breakpoint, click the ‘Step into’ button at the top. This will take us into the *if statement*.

And... presto! We can see the flag in the locals as **str0**, **str1**, and **str2**.
![image](/assets/img/BypassWriteUp/imageFive.png)

