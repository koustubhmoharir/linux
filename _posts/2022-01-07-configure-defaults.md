---
layout: post
title:  "Configure Defaults"
date:   2022-01-07 21:00:00 +0530
categories: basics
tags: 
---

## Setup a global user profile

This is done by placing a shell script in /etc/profile.d

```
$ sudo touch /etc/profile.d/custom.sh
$ sudo chmod +x /etc/profile.d/custom.sh
$ sudo vi /etc/profile.d/custom.sh
```
Enter the following content in the file and save it
```
#!/bin/sh

# This controls the default time style in the output of ls
export TIME_STYLE=long-iso

#This disables the ability to suspend the terminal with Ctrl + S and unfreeze it with Ctrl + Q
stty -ixon
```

