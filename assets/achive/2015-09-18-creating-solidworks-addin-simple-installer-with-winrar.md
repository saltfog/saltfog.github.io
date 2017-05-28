---
layout: post
title: Creating SolidWorks Add-in simple installer with WinRAR
description: One of the simple options and cheap to create a setup package (package installer) is by using using WinRAR SFX and Windows batch scripting.
keywords: solidworks addin, simple installer, winrar sfx
tags: [SolidWorks, Installation]
comments: true
---

When I was working as Application Engineer for one of the top SolidWorks Reseller local companies, I was assigned to develop a company's first SolidWorks Add-in. To make the installation process of the add-in assembly (DLL) as simple as possible, I opted to use WinRAR SFX and batch scripting to automate the assembly registration into Microsoft .NET Framework.

> **Prerequisite:** This process required a [WinRAR](http://www.rarlab.com/download.htm) software to be installed in my PC and prepared my add-in files or folders before I can use WinRAR SFX to create a package installer for my add-in.

These are the steps how I create my add-in package installer using the WinRAR SFX:-

### Step 1

![Step 01](http://i.imgur.com/B3LvCup.png)

I added all of my SolidWorks add-in files and folders to the WinRAR archive.

### Step 2

![Step 02](http://i.imgur.com/B9sW5YC.png)

Under General tab, I selected **Create SFX archive** in Archiving options and enter my Archive name. Other options are optional or I just leave it as defaults.

### Step 3

![Step 03](http://i.imgur.com/4mzS7A3.png)

Under Advanced tab, I clicked on **SFX options** button.

### Step 4

![Step 04](http://i.imgur.com/W2LNfxl.png)

In the **Advanced SFX options**, under General tab, I entered the Path to extract. In my case, I used `C:\IME` as the Absolute path for my SolidWorks add-in installation directory.

### Step 5

![Step 05](http://i.imgur.com/AzJSVxy.png)

Under Setup tab, I entered the filename that I want to run/execute after extraction is finished. In my case, `INSTALL64.BAT` is a batch file that I want it to run/execute after the package extraction.

### Step 6

![Step 06](http://i.imgur.com/K2b3WiE.png)

Under Advanced tab, I marked **Request administrative access** as checked since I need to register my SolidWorks add-in assembly into Microsoft .NET Framework. So, the user will be prompted to run the package installer with administrative access.

### Step 7

![Step 07](http://i.imgur.com/BuPgFLY.png)

Under Update tab, I checked **Extract and replace files** for Update mode and **Overwrite all files** for Overwrite mode. This will be useful when in the future time I deliver a new update or release of the newer version program, the new installation will replace and overwrite all of the old files.

### Step 8

![Step 08](http://i.imgur.com/IJ2GJUm.png)

Under Text and icon tab, here I entered the Title of SFX window, Text to display in SFX window and assign the SFX icon/logo.

### Step 9

![Step 09](http://i.imgur.com/gZNspH3.png)

Finally, under Module tab, I selected the SFX module that I want to use. In my case, I chose **Default.SFX** - Windows GUI RAR SFX.

### Step 10 (Final)

![Step 10](http://i.imgur.com/AWFrHVC.png)

Then, I pressed OK and OK again to finish it.

### Viola..

At the end, I got my SolidWorks add-in package installer (setup.exe) as shown in the picture below:

![Step 11](http://i.imgur.com/Drqud3n.png)

To test if the file is OK, I tried to run it (double-clicked), it showed up something like this (refer the picture below):

![Step 12](http://i.imgur.com/Jn8p8gs.png)

That means my SolidWorks add-in installer file is working!

### The Batch files

I used the batch scripting to automate some commands. If you noticed, there were two batch files in my SolidWorks add-in installation package; `INSTALL64.BAT` and `UNINSTALL64.BAT`. Both were used for the installation and uninstallation of my SolidWorks add-in. By using the batch scripting, I can register my SolidWorks add-in assembly into Microsoft .NET Framework so that it will work with the main SolidWorks software.

Here's the batch script inside the `INSTALL64.BAT` file:

```
@echo off

set TargetLocation=c:\IME\
set FolderToCopy=IMEInterX64
set FileToCopy1=UNINSTALL64.BAT

echo.
echo 1. Setup configuration data

start "" /d "%~dp0%FolderToCopy%" "addconfigs.exe"

echo #1 DONE
echo.
echo 2. Register assemblies

SET AssemblyPath="%TargetLocation%%FolderToCopy%\IMEInterX2_SwAddin.dll"
set FMWK=Framework64
IF EXIST "%Windir%\Microsoft.NET\%FMWK%\v4.0.30319" (set FMWKVersion=v4.0.30319) ELSE (set FMWKVersion=v2.0.50727)
echo Detected %FMWK% %FMWKVersion%
echo Running command...
@ECHO ON
"%Windir%\Microsoft.NET\%FMWK%\%FMWKVersion%\regasm" /codebase %AssemblyPath%
@ECHO OFF

echo #2 DONE

echo.
echo INSTALLATION COMPLETE
pause
```

`addconfigs.exe` is a mini program to create and load configuration data into Windows Registry.

`IMEInterX2_SwAddin.dll` is my SolidWorks add-in assembly that need to be registered (regasm) into Microsoft .NET Framework in order to work with the main SolidWorks software.

### The Uninstallation process

Basically, `UNINSTALL64.BAT` file is for removing all the SolidWorks add-in files and folders, deleting configuration data in the Windows Registry and finally the file will terminate itself. If I want to create the Uninstaller shortcut for my program, I can do that inside `INSTALL64.BAT` file, then link the shortcut to `UNINSTALL64.BAT` file when executed.
