---
layout: post
title:  "Working with the terminal"
date:   2022-01-08 21:00:00 +0530
categories: basics
tags: 
---

## Basic Concepts

The *terminal* is a window that receives user inputs (keyboard, mouse) and renders text output. It passes on most of the user inputs to a program called the shell. When using WSL (with or without Windows Terminal), the terminal is running on Windows, whereas the shell is running in Linux. The default shell on most Linux distributions is bash. It is possible to use a different shell such as zsh. It is possible for the terminal to interpret certain key sequences by itself and not pass them on to the shell. Typically, key sequences for Copy and Paste are handled by the terminal. The shell interprets user input as commands. When a command is submitted (typically by pressing Enter), the shell determines how to execute the command. Some commands may be handled by the shell directly. These are called builtins. Most commands launch an executable program as a child process of the shell. Typically this program runs in the foreground and the shell passes on most user input to the program while it is running instead of interpreting it by itself. Certain sequences like (Ctrl + C or Ctrl + Z) are interpreted by the shell to terminate the program or suspend it instead of passing them on to the program.

When the shell executes a builtin command, the shell parses the commandline into arguments to the command. When it launches an executable program, the parsing is done by the program and not by the shell. The shell still needs to do enough parsing to determine which program to launch, to do any redirection of input and output streams, and to expand parts of the command according to its rules. Thus, when the shell runs a program, it constructs a commandline which may be different from what is literally typed by the user, and then launches the program with this commandline.

## Shell Scripts

If the first line of an executable text file starts with a `#!` (called a shebang), the part of the line after the shebang is the name of the program that will be executed instead of the text file. Such programs use the file as an input to determine what to do. Commonly, the first line may be `#!/bin/sh`. `/bin/sh` is the main shell of the Linux distribution. Such files are shell scripts and the lines in the file are interpreted as a sequence of commands to be executed by the shell. Instead of `/bin/sh` it is possible to refer to a different shell and that shell will be used to run the script. On Ubuntu /bin/sh is a symbolic link to a shell called dash instead of bash. dash is [significantly lighter and faster](https://askubuntu.com/questions/976485/what-is-the-point-of-sh-being-linked-to-dash) than bash.

As an example, under default settings on Ubuntu, running a shell script that refers to `/bin/sh` after its shebang, starts the dash shell as a child process of bash, and the dash shell executes the commands in the script. In this case, bash is the interactive shell and dash is non-interactive. If the script referenced `/bin/bash` instead, there would be two instances of bash - interactive parent and non-interactive child. If the script executes another script, yet another shell instance will run.

Because shell scripts run in a child process instead of running directly in the interactive shell, local variables defined in the interactive shell are not available to the script. Variables can be added to the shell's environment. These variables are copied into the environment of the child process.

## Bash Shortcuts in vi mode

Use `set -o vi` to put the shell in vi editing mode if it isn't already in vi editing mode.

The shell is in insert mode by default. Switching between insert and command modes can be done in the same way as in vim (Esc for normal mode, i or a for insert mode).

## Bash Shortcuts in emacs mode

Ctrl + K cuts all text after the cursor and puts it in a buffer (anything that is cut goes into the buffer)  
Ctrl + U cuts all text before the cursor  
Ctrl + Y pastes the text from the buffer  
Ctrl + W cuts the word before the cursor  
Ctrl + Del cuts the word after the cursor  
Ctrl + XX jumps to the start of the text, pressing it again after typing something at the beginning of the command gets back to the previous position

## Working with command history

The Up and Down keys can be used to move up and down through previously executed commands.

If the hstr package has been installed, use `Ctrl + Alt + R` to search for commands in history. Press `Ctrl + K`, `Ctrl + J` or the arrow keys to navigate up and down, and type part of a command to filter the history. Press Tab to put the selected command on the prompt without executing it, or Enter to execute it directly. Press Esc to get back to an empty prompt.

If the hstr package has not been installed, use `Ctrl + R` to enter search mode. Start typing the command. Move through the results by pressing `Ctrl + R` to go further back or `Ctrl + S` to move forward (after having gone back).  
Press `Ctrl + O` or Enter to run the command.
Hit `Ctrl + J` or Esc or the left / right arrow keys to get back to command mode with the text of the selected command on the prompt which can be edited further.
Press `Ctrl + G` to exit the search without any text on the prompt.

`!!` runs the last command  
`sudo !!` runs the last command with sudo

`history | grep 'some search text'` can be used to output a list of commands from the command history containing `some search text`. The single quotes can be skipped if the search is for a single word. The output shows a command number and the command. `!num` can be used to run the command with the number `num`.

## Editing a complex command

In vi mode, press Esc and then v to open the command in vi. Quitting vi will execute the command.

In emacs mode, `Ctrl + XE` opens an editor with the text on the console. Once editing is complete, exiting the editor with a save executes the command.

To exit the editor without submitting the command, delete the text in the editor first.

## Getting help

The help command can be used to get help on any builtin command provided by the shell. For example `help echo` prints help on the terminal.

Many executables have a `--help` option. For example, `ls --help` can be used to print help. However this can be cumbersome to read. A better approach is to use `man executable_name` to get the manual page for standard executables. When using `man` the interface is similar to vi. To search within this interface, press `/` followed by the search text and then Enter. Press `n` for the next search result and `Shift + n` for the previous one. To follow links within the man page, move the cursor within the link and press `Ctrl + ]`. To go back, press `Ctrl + O`

## Viewing text files

Text files can be viewed conveniently with the less command. The interface is very similar to the one provided by man. For example `less /etc/vim/vimrc` shows the content of this file in a readonly mode. This is very convenient when there is no intent to modify the file. Press q to exit the view. To start editing the file directly from vim, press `v` to open it in vim.

## Editing command output

The output of a command can be piped into vi with `command | vi -`. This can be very useful when the command output is large.