---
layout: post
title: Easiest way to install Jekyll on Windows using Chocolatey
description: Installing Jekyll on Windows from Command Prompt with Chocolatey is very simple way to do and faster too.
keywords: jekyll installation, windows os, chocolatey, command prompt
tags: [Jekyll, Chocolatey]
comments: true
---

[Chocolatey](https://chocolatey.org/) is a package manager for Windows, takes advantage of PowerShell to provide automated software management instructions and Chocolatey's built-in module to run complex tasks into one line function calls.

### Install Jekyll on Windows using Chocolatey

First, you need to install Chocolatey and the easy way to do is from your command prompt. Just copy-and-paste this script into your command prompt (run with Administrator access) and hit Enter:

```
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
```

After finished with Chocolatey installation, close the command prompt and re-open it (run with Administrator access) to start installing [Ruby](https://chocolatey.org/packages/ruby) using `choco install` command:

```
choco install ruby -y
```

After finished with Ruby installation, now is the time to install [Jekyll](https://jekyllrb.com/), the same way to install Ruby, close and re-open the command prompt (run with Administrator access), but using `gem install` command like this:

```
gem install jekyll
```

Finished! Now, we can use standard Jekyll commands to create a new site and serve it with such commands below:

```
jekyll new myblog
cd myblog
jekyll serve
```

Credit to [David Burela](https://davidburela.wordpress.com/2015/11/28/easily-install-jekyll-on-windows-with-3-command-prompt-entries-and-chocolatey/) for this tips.

Additionally, I have installed another program in my Windows PC known as [FileMenu Tools](https://www.lopesoft.com/index.php/en/download/filemenu-tools). It's a context-menu program where I can easily run the command prompt at the current opened folder (or any folder) just by right-click on it and select "Command Line From Here". It has many more useful utilities, but opening the command prompt is the one I use the most.

Happy Jekyll-ing and good luck!
