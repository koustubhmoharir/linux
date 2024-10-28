---
layout: post
title:  "Configure Defaults"
date:   2022-01-07 21:00:00 +0530
categories: basics
tags: 
---

## Install the hstr package

The [hstr package](https://github.com/dvorka/hstr/) can be used to work with the command history in a very intuitive visual manner. The link contains installation instructions. The initialization script below binds `Ctrl + Alt + R` to this package. This can be used instead of `Ctrl + R` on the interactive shell.

## Setup a script global user profile

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

# This improves tab completion
bind 'set show-all-if-ambiguous on'
bind 'TAB:menu-complete'
bind '"\e[Z":menu-complete-backward'        #\e[Z is the code for Shift + Tab

#This disables the ability to suspend the terminal with Ctrl + S and unfreeze it with Ctrl + Q
stty -ixon

# HSTR configuration - add this to ~/.bashrc
alias hh=hstr                    # hh to be alias for hstr
export HSTR_CONFIG=hicolor       # get more colors
shopt -s histappend              # append new history items to .bash_history
export HISTCONTROL=ignorespace   # leading space hides commands from history
export HISTFILESIZE=10000        # increase history file size (default is 500)
export HISTSIZE=${HISTFILESIZE}  # increase history size (default is 500)
# ensure synchronization between bash memory and history file
export PROMPT_COMMAND="history -a; history -n; ${PROMPT_COMMAND}"
# if this is interactive shell, then bind hstr to Ctrl-r (for Vi mode check doc)
if [[ $- =~ .*i.* ]]; then bind '"\C-r": "\C-a hstr -- \C-j"'; fi
# if this is interactive shell, then bind 'kill last command' to Ctrl-x k
if [[ $- =~ .*i.* ]]; then bind '"\C-xk": "\C-a hstr -k \C-j"'; fi
```
