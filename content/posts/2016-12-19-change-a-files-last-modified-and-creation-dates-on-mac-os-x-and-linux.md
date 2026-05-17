---
title: "Change a File's Last Modified and Creation Dates on Mac OS X and Linux"
date: 2016-12-19
draft: false
categories:
  - how-to
tags:
  - linux
  - how-to
slug: change-a-files-last-modified-and-creation-dates-on-mac-os-x-and-linux
---

### Date Format Key

- **YYYY** — The year (the first two digits/century can be omitted).
- **MM** — The month of the year, from 1 to 12.
- **DD** — The day of the month, from 1 to 31.
- **hh** — The hour of the day, from 0 to 23.
- **mm** — The minute of the hour, from 0 to 59.

[Go to answer for Mac OS High Sierra and newer](#sierra)

[Go to answer for Mac OS El Capitan and older](#elcapitan)

## How to change the "date modified" attribute of a file in Mac OS High Sierra and newer? {#sierra}

Unfortunately, new versions of Mac OS have different version of `touch` command, try these steps:

1. Open up the Terminal application (*Applications/Utilities/Terminal.app*) or if you prefer use [iTerm](http://iterm.sourceforge.net/).
2. Type this into Terminal (without hitting enter/return) replacing *YYYYMMDDhhmm* with the desired date information:

```bash
touch -m -t YYYYMMDDhhmm
```

3. Open a Finder window and locate the file you wish to modify and drag and drop it into the Terminal window. Here's an example of what should be typed into the Terminal at this point:

```bash
touch -m -t 200801120000 /Volumes/Mac\ HD/Pictures/somefile.jpg
```

*200801120000* in the example above represent "January 12, 2008 12:00 AM" or my 21st birthday. Make sure to change *200801120000* to the date you want and to replace */Volumes/Mac\ HD/Pictures/somefile.jpg* with the proper path to the file you wish to alter the date for.

4. If all is in order *press the return key*.

### How to change the "date modified" attribute recursively?

```bash
find ./folder/ -type f -exec touch -m -t 200801120000 {} \;
```

### How to change creation date too?

Just remove **m** argument:

```bash
touch -t 200801120000
```

**Alternatively**, you can install xcode command line tools, and follow all steps:

```bash
xcode-select --install
```

Then you can use it's command to change creation date, date syntax is MM/DD/YYYY HH:MM:SS (where HH is hours in 24h format):

```bash
setfile -d "02/28/2016 13:21:59" FILENAME.PNG
```

## How to change the "date modified" attribute of a file in Mac OS El Capitan and older? {#elcapitan}

1. Open up the Terminal application (*Applications/Utilities/Terminal.app*) or if you prefer use [iTerm](http://iterm.sourceforge.net/).
2. Type this into Terminal (without hitting enter/return) replacing *YYYYMMDDhhmm* with the desired date information:

```bash
touch -mtYYYYMMDDhhmm
```

3. Open a Finder window and locate the file you wish to modify and drag and drop it into the Terminal window. Here's an example of what should be typed into the Terminal at this point:

```bash
touch -mt200801120000 /Volumes/Mac\ HD/Pictures/somefile.jpg
```

*200801120000* in the example above represent "January 12, 2008 12:00 AM" or my 21st birthday. Make sure to change *200801120000* to the date you want and to replace */Volumes/Mac\ HD/Pictures/somefile.jpg* with the proper path to the file you wish to alter the date for.

4. If all is in order *press the return key*.

### How to change the "date modified" attribute recursively?

```bash
find ./folder/ -type f -exec touch -mt200801120000 {} \;
```

### How to change creation date too?

Just remove **m** argument:

```bash
touch -t200801120000
```

## What's about Linux?

It's the same but with additional space:

```bash
touch -mt YYYYMMDDhhmm
```

and recursive one:

```bash
find ./folder/ -type f -exec touch -mt 07011200 {} \;
```
