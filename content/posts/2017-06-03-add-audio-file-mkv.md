---
title: "Add audio file to mkv"
date: 2017-06-03
draft: false
categories:
  - how-to
tags:
  - linux
  - how-to
slug: add-audio-file-mkv
---

First you need to install mkvtoolnix

```bash
apt-get install mkvtoolnix
```

The command is:

```bash
mkvmerge -o output-with-sound.mkv -A input-video.mkv soundfile.mp3
```

Or you can use batch variant:

```bash
find . -maxdepth 1 -name "*.mkv" -type f | while read NAME ; do mkvmerge -o ./output/"${NAME%.*}".mkv -A "${NAME%.*}".mkv ./audio/"${NAME%.*}".mka ; done
```
