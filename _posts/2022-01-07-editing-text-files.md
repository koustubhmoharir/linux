---
layout: post
title:  "Editing text files"
date:   2022-01-07 18:42:00 +0530
categories: basics
tags: 
---

## Using Windows tools

When using wsl, it is possible to use local windows programs to edit files. For example `Notepad++.exe path_to_file` works.


## Using vim

Using Windows tools is a crutch as these are not going to be available in other environments. It is necessary to take the time to learn vim. Most of the content below is taken from [Beginner's guide to vim](https://www.linux.com/training-tutorials/vim-101-beginners-guide-vim/). It takes some getting used to, but it is easy to see that vim is a [powerful editor](https://www.barbarianmeetscoding.com/blog/boost-your-coding-fu-with-vscode-and-vim).

Ubuntu has a restricted version of vim. Run `sudo apt-get install vim-gtk` to replace it with a version with more features.

The `vi path_to_file` command opens the vim editor.

vim opens in Command mode. In this mode, pressing keys results in commands being invoked instead of characters being inserted into the document. Press `i` to enter Insert mode at cursor position (or `a` to enter Insert mode after character at cursor position) and start typing. Press `Esc` to go back to command mode.

Insert mode is highlighted at the bottom.

`h` (or left arrow), `j` (or down arrow), `k` (or up arrow), `l` (or right arrow) to move around.  
Prefix with a number to move in multiples of a character or line. `5j` moves down by 5 lines.

`w` moves forward one **w**ord  
`b` moves **b**ackward one word  
`0` moves to start of line  
`$` moves to end of line

`gg` moves to the first line of the file  
`Shift + g` moves to the last line of the file

`x` or `Del` deletes one character  
`dd` deletes entire line  
`dw`, `db`, `d0`, `d$` combines delete with w, b, 0, $ respectively
`Shift + d` deletes to the end of the line not including newline

`u` - Undo  
`Ctrl + r` - Redo

`/text + Enter` to search for text. n to move to next search result. N to move to previous result.

<aside>If the selection makes the text unreadable, press <code>:set background = dark</code> and then Enter to change vim's background to dark</aside>
`v` to enter Visual selection mode. Navigate to select.  
`y` to copy  
`p` to paste after the character at the cursor position  
`Shift + p` to paste at cursor position  

Press `:` to enter a mode where a command can be typed in at the bottom and submitted by pressing Enter or cancelled by Esc

`:w` to save work (changes)  
`:w` new_file_name to save a copy  
`:wq` to save work and quit  
`:q` to quit if there are no changes  
`:q!` to quit and discard any unsaved changes

### Saving a file without write permission

`:w` cannot write a file without write permission. When such a file is opened, vim shows \[readonly] at the bottom. However, it is possible to still edit such a file and save it with sudo. To do this, use `:w !sudo tee %`

## Using the system clipboard

Getting vim to copy and paste from the system clipboard seems tricky. The following solution works for wsl

Create a ~/.vimrc file and enter the code below
```
" Not sure why this is required. Probably some broken integration with Windows Terminal
set background=dark

" WSL yank support
let s:clip = '/mnt/c/Windows/System32/clip.exe'  " change this path according to your mount point
if executable(s:clip)
    augroup WSLYank
        autocmd!
        autocmd TextYankPost * if v:event.operator ==# 'y' | call system(s:clip, @0) | endif
    augroup END
endif

```

Content copied with `y` from vim is put onto the Windows clipboard (Do not exit vim before pasting it elsewhere).
Content from the Windows clipboard can be pasted into vim with `Ctrl + Shift + v` instead of `p`.

## Other useful commands

`.` repeats the last command

`o` enters insert mode in a new line after the current one  
`Shift + o` does it before the current line

`yy` copies an entire line (including the newline character at the end). In general doubling any operator applies it to the entire line.  
`y3y` copies 3 lines starting from the current line  
`yaw` copies an entire word with the whitespace after it when the cursor is anywhere within it  
`yiw` copies an entire word without the whitespace after it  
`ya"` copies quoted text when the cursor is anywhere within it  
`yi"` copies quoted text when the cursor is anywhere within it  
`yab` copies text in matching parenthesis including them  
`yib` copies text within matching parenthesis  
`yw` copies a word from the cursor to the right including whitespace  
`ye` copies a word from the cursor to the right excluding whitespace  
`yb` copies a word from the cursor to the left excluding whitespace  
`y$` copies from cursor to end of the line (excluding the newline)  
`y0` copies from cursor to beginning of the line  
In all of the commands above, `y` can be replaced with `d` to delete, `c` to change (delete and enter insert mode)

`Shift + v` starts visual line selection  

`Ctrl + v` starts visual block selection. After selecting a block, any of the following can be done:
Press `Shift + i` to type something at the beginning of the block and then press Esc to apply it on all lines in the block.  
Press `Shift + a` to type something at the end of the block and then press Esc to apply it on all lines in the block.  
Press `p` or `Shift + p` to paste at the end or beginning of the block.  

`gUw` converts to upper case from the cursor to the end of the word  
`guw` converts to lower case from the cursor to the end of the word  
In the commands above, w can be replaced by any "motion" (like b, $, 0, etc)

Visual select and then `~` toggles the case, `U` converts to upper and `u` converts to lower.

`!!bash` followed by enter executes the current line in bash and *replaces* it with any output written to bash.  
To retain the line, press `yyp` before `!!bash`

To execute multiple lines in bash and retain them, press `Shift + V` to enter visual line selection, use arrow keys to select lines, `Shift + p` to paste, `gv` to reselect, and `!bash`
