---
layout: post
title: Asynchronous PowerShell Scripter
description: Asynchronous PowerShell Scripter is simply a tool built in C# WPF to easily manage, asynchronously execute or to learn PowerShell scripts.
keywords: c# programming, asynchronous powershell scripter, learn powershell, manage powershell preset scripts, powershell scripts, execute powershell asynchronously
tags: [CSharp, WPF, PowerShell]
comments: true
---

Asynchronous PowerShell Scripter is a .NET C# WPF-version application for executing PowerShell script asynchronously with few following interesting features;-

- save new or remove PowerShell preset scripts
- import PowerShell script file directly
- drag-n-drop PowerShell script file onto Script Editor
- export selected lines or all lines output into a text file.

### Screenshots

![Loading all preset scripts](http://i.imgur.com/jwH7z94.png)

Figure 1: During application startup or adding/removing preset script will display this Loader overlay to show that the application is loading/reloading all preset script files from `presets` folder.


![Application GUI](http://i.imgur.com/QIiIiPb.png)

Figure 2: This is how the application is looked like, the GUI.


![Executing PowerShell script](http://i.imgur.com/aumSwRz.png)

Figure 3: Example of executing PowerShell script from selected preset script. The application can be maximized for better Output view.


![Selecting output lines](http://i.imgur.com/wkeT8Yo.png)

Figure 4: Example of selecting output lines for exporting to a text file.


![Save current script as new preset script](http://i.imgur.com/cJBS41G.png)

![Define new preset script title](http://i.imgur.com/DGwi2J7.png)

Figure 5 & 6: Example of saving current script as a new preset script.


![Importing PowerShell script file](http://i.imgur.com/D6rnHDz.png)

Figure 7: Menu to import script from PowerShell script file (.ps1).

### Preset Scripts

Preset scripts are saved in the plain text file format in "presets" folder where the title of the script is the filename of the text file itself. This makes thing easier to manually modify the script or to add a batch of scripts WITHOUT needing to run the application. It's more manageable, easy to backup or synchronize with the cloud storage service.

### Source Code and Downloads

Asynchronous PowerShell Scripter source code is available on [GitHub](https://github.com/heiswayi/AsyncPowerShellScripter) repository and licensed under [MIT License](https://github.com/heiswayi/AsyncPowerShellScripter/blob/master/LICENSE.md). The source code is personally maintained by me, however if anyone interested to contribute, the person is free to do so; just fork out the repository, make changes and pull request.

To download the binary package (executable file), please visit Asynchronous PowerShell Scripter [release page here](https://github.com/heiswayi/AsyncPowerShellScripter/releases).

### Credits

This project is originally inspired from or a derivative works of [Asynchronously Execute PowerShell Scripts from C#](https://www.codeproject.com/Articles/18409/Asynchronously-Execute-PowerShell-Scripts-from-C) by jpmik.
