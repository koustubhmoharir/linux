---
layout: post
title:  "Basics of vim"
date:   2024-11-16 19:44:00 +0530
categories: basics
tags: 
---

## Using Windows tools

When using wsl, it is possible to use local windows programs to edit files. For example `Notepad++.exe path_to_file` works.


## Why vim?

Using Windows tools is a crutch as these are not going to be available in Linux environments other than WSL. Variants of vi or vim can be expected to be installed by default on every Linux distribution. Moreover, vim is actually an extremely capable editor. Hence it is a very good idea to take the time to learn vim. Ubuntu has a restricted version of vim by default. Run `sudo apt install vim-gtk` to replace it with a version with more features.

## Absolute basics

From the terminal, `vi filepath` opens vim.

vim is a *modal* editor. It opens in command (or normal) mode where keys correspond to commands. To input text by typing, it needs to be put into insert mode. A useful way to do this is to press `a` (for append). Pressing Esc puts it back into command mode. Make sure that the cursor renders as a solid rectangle that takes up a whole letter when in command mode and as a thin line between letters when in insert mode. Pressing a puts the insertion point to the right of the cursor (hence append). Pressing `i` (for insert) also puts vim into insert mode with the insertion point to the left of the cursor.

Arrow keys (with or without Ctrl), Page Up, Page Down, etc all work as in other text editors. While there are better alternatives to these keys, starting with these can help in getting work done.

When `Ctrl+G` is pressed vim displays the file name in the status bar along with a [Modified] indicator if it has unsaved changes.

To exit vim with or without saving the buffer (the text being edited), vim must be in command mode. Press Esc to ensure it is in command mode. Then press `:` to bring up a prompt and then `wq` and `Enter` to save and quit. `w` stands for write the buffer to a file. `q` stands for quit. Most commands can be issued directly, but some commands (like `w` and `q`) can only be entered in a prompt with `:`. Enter submits a command in the prompt.

Submitting `q!` force quits the buffer without writing it.

Sometimes, the editor may not render correctly, especially when returning to the editor from fzf.vim commands. Pressing `Ctrl+L` usually fixes the rendering.

