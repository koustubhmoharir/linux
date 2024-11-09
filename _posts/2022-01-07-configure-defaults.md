---
layout: post
title:  "Configure Defaults"
date:   2022-01-07 21:00:00 +0530
categories: basics
tags: 
---

## Enable vi mode for shell commands

vi editing mode can be enabled on the shell instead of emacs mode which is the default. This is done by editing / creating the ~/.inputrc file and entering the following content in it.

```
set editing-mode vi
set show-mode-in-prompt On
set vi-cmd-mode-string "((cmd))\1\e[2 q\2"
set vi-ins-mode-string "((ins))\1\e[5 q\2"
```

On a shell, the editing mode can be switched with `set -o vi` and `set -o emacs` at any time. It makes sense to match the editing mode for commands and text files to avoid having to learn both. This guide assumes the use of vim.

## Install the hstr package

The [hstr package](https://github.com/dvorka/hstr/) can be used to work with the command history in a very intuitive visual manner. The link contains installation instructions. The initialization script below binds `Ctrl + Alt + R` to this package. This can be used instead of `Ctrl + R` on the interactive shell when it is emacs mode.

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
# if this is interactive shell, then bind hstr to Ctrl-Alt-r
if [[ $- =~ .*i.* ]]; then bind '"\e\C-r":"\e^ihstr -- \n"'; fi
```

The default prompt in bash is quite long and does not leave enough space for long commands especially when the current directory is also long. To split the prompt into two lines, with the first line showing information, and the second line showing the prompt, edit the ~/.bashrc file. Look for the lines that set `PS1` and insert a `\n` before the final `\$`
```
if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\n\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\n\$ '
fi
```