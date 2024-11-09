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

Ubuntu has a restricted version of vim. Run `sudo apt install vim-gtk` to replace it with a version with more features.

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
`x` or `:wq` to save work and quit  
`:q` to quit if there are no changes  
`:q!` to quit and discard any unsaved changes

### Saving a file without write permission

`:w` cannot write a file without write permission. When such a file is opened, vim shows \[readonly] at the bottom. However, it is possible to still edit such a file and save it with sudo. To do this, use `:w !sudo tee %`

## Configuring the cursor to match the mode

In the /etc/vim/vimrc.local file created in the last section, add the following
```
" set cursor to be a blinking line in insert mode
let &t_SI .= "\<Esc>[5 q"
" set cursor to be a solid box in normal mode
let &t_EI .= "\<Esc>[2 q"
" set cursor to be a blinking box in replace mode
let &t_SR .= "\<Esc>[1 q"
" set cursor to replace mode when leaving buffer in all windows
" revert cursor back to normal mode when entering buffer
autocmd BufEnter * execute 'silent !echo -ne "' . &t_EI . '"'
autocmd BufWinLeave * execute 'silent !echo -ne "' . &t_SI . '"'
```
This makes the cursor more visible, and also makes it easier to see if the editor is in normal or insert or replace mode. The `t_SI`, `t_EI`, and `t_SR` are vim options, and somewhat confusingly, a let statement with `&` changes the value of an option!

In the ~/.vimrc file, place the following content
```
set belloff=all
set tabstop=4
set shiftwidth=4 smarttab
set expandtab
set autoindent
set smartindent
set whichwrap+=<,>,[,],h,l
set relativenumber number
set clipboard^=unnamedplus
set foldcolumn=3
```

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

Use `Ctrl + G` to show the current file name, and `1` followed by `Ctrl + G` to show the file path (not expanded) in the status bar