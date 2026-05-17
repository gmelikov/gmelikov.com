---
title: "Batch lossless optimize images in Linux, Mac OS and Windows"
date: 2017-06-01
draft: false
categories:
  - how-to
tags:
  - linux
  - how-to
slug: batch-lossless-optimize-images-linux-mac-os-windows
---

### Linux and Mac

You need optipng and jpegoptim for this. In Debian/Ubuntu just run this command as superuser:

```bash
apt-get install optipng jpegoptim
```

For batch optimization of png and jpeg images, use these commands:

```bash
find . -iname '*.png' -print0 -exec optipng -o7 -strip all  "{}" \;
find . -iregex '.*\.\(jpg\|jpeg\|jpe\|jif|jfif|jfi\)$' -exec jpegoptim --all-progressive --strip-all --strip-com --strip-exif --strip-iptc --strip-icc "{}" \;
```

Multi-threaded variant (example for 4 cores):

```bash
find . -iname '*.png' -print0 | xargs -0 -n 1 -P 4 optipng -o7 -strip all
find . -iregex '.*\.\(jpg\|jpeg\|jpe\|jif|jfif|jfi\)$' -print0 | xargs -0 -n 1 -P 4 jpegoptim --all-progressive --strip-all --strip-com --strip-exif --strip-iptc --strip-icc
```

### Notes

optipng version older than 7 doesn't work with -strip all, remove it from command in this case.

For fast png optimization change **-o7** to **-o2**, compress ratio will be just a bit worse, but it will work more faster.

Jpeg optimization is lossless, but you can use **--max=90** to change compress ratio (100 for lossless compress, less is better compress but worse quality. My opinion — 90 is the best for web, difference with original image is hard to find.)

### Microsoft Windows

You can use same [optipng.exe](http://optipng.sourceforge.net/) and [jpegoptim.exe](https://bitbucket.org/rorgoroth/jpegoptim-for-windows/downloads) commands, but foreach file iteration from Windows must be used. Use this code, paste it to text file with name optimize.bat:

```bat
@ECHO OFF
:Loop
IF "%1"=="" GOTO Continue
jpegoptim.exe --strip-all %1
optipng.exe -o7 -strip all %1
SHIFT
GOTO Loop
:Continue
```

, then just drag images on this .bat file to optimize them.
