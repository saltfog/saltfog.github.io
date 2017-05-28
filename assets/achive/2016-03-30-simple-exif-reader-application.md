---
layout: post
title: Simple Exif Reader application
description: Simple Exif Reader application is built in C# WPF to extract EXIF data from an image file for photography info purpose.
keywords: exif reader, exif data, extract metadata, histogram analysis, c# wpf
tags: [EXIF, CSharp, WPF]
comments: true
---

### What is EXIF data?

Exchange Image File Format or EXIF data is typically comprised of a range of settings such as ISO speed, shutter speed, aperture, white balance, camera model and make, date and time, lens type, focal length and much more. Nowadays, every modern digital camera has the capability to record this information, along with many other camera settings, right into the photographs.

Since I have been clicking my camera shutter a lot these days, so I need a simple application for me to easily read my photograph EXIF data. Then, I just created one called Heiswayi Nrird Exif Reader in C# WPF, a simple tool to easily extract the EXIF data from an image file.

### Screenshot

![Exif Reader v1.1](http://i.imgur.com/yAls0JU.png)

### Supported features

- Load image file from local disk or remotely via FETCH URL.
- Extract metadata (EXIF data) of the image file.
- Preview the loaded image file with histogram analysis.
- Export the EXIF data to a text file.

### Source Code and Downloads

The original intention of this program is as one of my photography tools, and I think I would like to share the source code and make this program available publicly so someone somewhere can also benefits from it.

You may download the latest binary package (executable file) on [**release page**](https://github.com/heiswayi/ExifReader/releases) or you can fork out its **source code** from my [GitHub](https://github.com/heiswayi/ExifReader) repository.
