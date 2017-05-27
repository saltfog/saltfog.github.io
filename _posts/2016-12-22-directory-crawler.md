---
layout: post
title: Directory Crawler
description: A lightweight application to crawl and list out all accessible directories recursively within a target directory and output the results into a text file.
keywords: c# programming, console application, directory crawler, get directories
tags: [CSharp, Console Application]
comments: true
---

Directory Crawler is a lightweight console application built in .NET C# to crawl and list out all accessible directories and subdirectories recursively within an assigned target directory, then output the results of directory list into a text file.

I created this program as I need to monitor some shared directories within a local network.

### Screenshot

Here's how the program looked like and works, in animated GIF:

![DirectoryCrawler](http://i.imgur.com/e3mrlmx.gif)

After the program finished running, an output text file will be created within the program base folder and if you open the text file, this is how it looked like; a list of accessible directories within the assigned target directory.

_Note: Creating an output file sometimes might be time-consuming depending on PC performance and number of crawled directories (usually more than 1000+ directories)._

Example (opened using Notepad2-mod app):

![Output file](http://i.imgur.com/qaUZ9n3.png)

### Source Code and Downloads

The source code is available on my [GitHub](https://github.com/heiswayi/DirectoryCrawler) repository and compiled using Visual Studio 2015 Community. The source code is released under [MIT](http://heiswayi.github.io/mit-license) license. The latest binary package can be download from the [release page](https://github.com/heiswayi/DirectoryCrawler/releases).
