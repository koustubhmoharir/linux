---
layout: post
title:  "Common Commands"
date:   2022-01-07 18:31:37 +0530
categories: basics
tags: 
---

## Syntax

Most commands have options that can be specified as either
- a hyphen followed by single letter (short options)
- double hyphen followed by the full option name which may be a single word or multiple words with hyphens in between but no spaces (long options)

In the case of single letter options, multiple options may be combined with a single hyphen. In other words, a single hyphen means that the letters following it are interpreted as separate options, and a double hyphen means that the letters following it are interpreted as a single option.

Options are case-sensitive.

The list below is taken from [here](https://www.hostinger.in/tutorials/linux-commands). It does not cover the full functionality of the commands in general, only the common use cases.

## clear

`clear` clears the terminal

## echo

`echo $USER` prints the value of the variable with name USER

## pwd

`pwd` prints the current working directory.  
`pwd -L` prints the value from the environment and it may have symlinks in it.  
`pwd -P` prints the full physical path without symlinks.  
Default option when not specified is implementation dependent.

## cd

Changes the current working directory

`cd` moves to the home directory  
`cd ..` moves one directory up. The space is important.  
`cd -` moves to the previous working directory and prints it too. This is like the back navigation button. Other variants of this command don't print anything.  
cd can be called with an absolute path or a path relative to the current working directory.  

Execute permission on a directory is required to cd into it.

## ls

Produces a directory listing of the current working directory or a specified absolute or relative path.

`ls -phlA` or `ls -phlAt` or `ls -phlAS`

`-p` shows trailing slashes after directory names  
`-h` shows sizes in a human readable format in K, M, G etc  
`-l` shows detailed tabular output instead of a horizontal list of just names  
`-A` shows entries starting with a dot also  
Entries are sorted alphabetically, unless one of the following options is provided  
`-t` sorts by modification time (latest first)  
`-S` sorts by size (largest first).

## stat

Shows information about a file or directory

`stat -c '%a %U %G' file_path` prints the numerical representation of the permissions (%a) along with owning user (%U) and group (%G) names  
`stat -c '%A %U %G' file_path` prints the text representation with file type (- for files, d for directories) as the first character (just like `ls -l`) along with owning user and group names

Lower case `u` and `g` show ids instead of names for user and group.

`%s` can be included in the format to show size. 
`%y` can be included in the format to show last modification time. 


## cat

`cat filepath` prints the contents of the file
`cat > filepath` starts creating a new file. The contents of the file can be typed in the terminal. Ctrl + C ends the contents.

To view or create a file, using an editor like vi is a better option. This command is probably overused.
