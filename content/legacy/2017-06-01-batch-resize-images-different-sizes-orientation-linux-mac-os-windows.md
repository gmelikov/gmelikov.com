---
title: "Batch resize images with different sizes for each orientation in Linux, Mac OS and Windows"
date: 2017-06-01
draft: false
categories:
  - legacy
tags:
  - linux
  - how-to
slug: batch-resize-images-different-sizes-orientation-linux-mac-os-windows
build:
  list: false
---

You need imagemagick for this. In Debian/Ubuntu just run this command as superuser:

```bash
apt-get install imagemagick
```

### Linux and Mac

Just use this script:

```bash
#!/bin/bash
I=0
for f in *.jpg
do
(( I++ ))
r=$(printf "%03d" $I)
 read w h <<< $(convert "$f" -ping -format "%w %h" info: )
 if [ $w -gt $h ]
 then
  echo "$f is $h tall and $w wide (landscape)"
  convert "$f[800x>]" $r.jpg
 else
  echo "$f is $h tall and $w wide (portrait)"
  convert "$f[395x>]" $r.jpg
 fi
done
```

, where **800x>** is settings to resize, more about them [here](http://www.imagemagick.org/script/command-line-processing.php#geometry).

Most used are:

- **800x>** = if width more than 800px then shrink width to 800px with original scale.
- **x800>** = if height more than 800px then shrink height to 800px with original scale.

### Microsoft Windows

You can use same imagemagick command, but foreach file iteration from Windows must be used.

Universal command to resize all images in folder:

```
convert '*.jpg[x600>]' %03d.jpg
```
