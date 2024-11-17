---
layout: post
title:  "Improving usability"
date:   2024-11-16 10:56:00 +0530
categories: basics
tags: 
---

The default settings on a new Linux installation are not very useful, probably for the sake of maintaining backward compatibility. However, by modifying config files, and installing additional tools, usability can be improved enormously.

## Enable vi mode for shell commands

vi editing mode can be enabled on the shell instead of emacs mode which is the default. This is done by editing / creating the ~/.inputrc file and entering the following content in it.

```
set editing-mode vi

# Customize the prompt to show the editing mode. Also switch the cursor shape to suit the mode.
set show-mode-in-prompt On
set vi-cmd-mode-string "((cmd))\1\e[2 q\2"
set vi-ins-mode-string "((ins))\1\e[5 q\2"
```

On a shell, the editing mode can be switched with `set -o vi` and `set -o emacs` at any time. It makes sense to match the editing mode for commands and text files to avoid having to learn both. This guide assumes the use of vim. However, at least in bash, the vi mode is not as powerful as vim. It is possible to open a command for editing in vim by pressing v.


## Install the hstr package

The [hstr package](https://github.com/dvorka/hstr/) can be used to work with the command history in a very intuitive visual manner. It can be [installed] with `sudo apt install hstr`.

## Setup a script global user profile

This is done by placing a shell script in /etc/profile.d
The lines below create the file, make it executable, and open it in vi.

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


# HSTR configuration - based on https://github.com/dvorka/hstr/blob/master/CONFIGURATION.md 
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

The initialization script below binds `Ctrl + Alt + R` to this package. This can be used instead of `Ctrl + R` on the interactive shell.

## Customizing the prompt in bash

The default prompt in bash is quite long and does not leave enough space for long commands especially when the current directory is also long. To split the prompt into two lines, with the first line showing information, and the second line showing the prompt, edit the ~/.bashrc file. Look for the lines that set `PS1` and insert a `\n` before the final `\$`

```
if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\n\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\n\$ '
fi
```

## Configuring the cursor in vim to match the mode

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

## Configuring a plugin manager and other default settings for vim

The ~/.vimrc file can be configured to auto-install the vim-plug plugin manager. The list of plugins can also be specified in this same file and Plug installs them at startup. [Plug](https://github.com/junegunn/vim-plug?tab=readme-ov-file) also adds some commands to vim to update the plugins when desired.

`:PlugInstall` installs the listed plugins  
`:PlugUpdate` updates or installs the listed plugins  
`:PlugDiff` reviews the changes since the last update  

The configuration shown below adds the vim-polyglot, fzf, fzf.vim plugins  
In the ~/.vimrc file, place the following content
```
" Enable the mouse in all modes
set mouse=a

" Without this, dragging window separators doesn't work
set ttymouse=sgr

set belloff=all
set tabstop=4
set shiftwidth=4 smarttab
set expandtab
set autoindent
set smartindent

" Without this basic horizontal movements don't wrap
set whichwrap+=<,>,[,],h,l

" Don't wrap in the middle of a word
set linebreak
" Display the last line even if it cannot be displayed fully
set display+=lastline

" This shows the absolute line number aligned left and relative line numbers aligned right in the left margin
set relativenumber number

set showcmd

" remap the Enter key to insert a newline before the cursor while remaining in normal mode. Shift Enter will insert a newline after the cursor.
nnoremap <CR> i<CR><Esc>
nnoremap <S-CR> a<CR><Esc>

" remap Alt k, Alt j, Alt Up, Alt Down to move the current line up and down
" Due to some limitations of terminal interaction, the input sequence is
" specified as the raw input obtained by pressing Ctrl v followed by the
" actual key sequence to be mapped.
nnoremap k :m -2<CR>
nnoremap j :m +1<CR>
nnoremap [1;3A :m -2<CR>
nnoremap [1;3B :m +1<CR>

" Remap up and down arrow keys to move through wrapped lines in both insert
" and normal modes. The idea is that hjkl keys are for logical navigation and
" arrow keys are for visual navigation.
nnoremap <silent> <Up> gk
nnoremap <silent> <Down> gj
inoremap <silent> <Up> <C-o>gk
inoremap <silent> <Down> <C-o>gj

" Put yanked (copied) content on the system clipboard automatically
set clipboard^=unnamedplus

set foldcolumn=3

" Allow buffers to be hidden without prompting to write them first
set hidden

" When a window is split, set the new window to appear at the bottom or right
set splitbelow
set splitright

" Code to install vim-plug (plugin manager for vim) automatically
" https://github.com/junegunn/vim-plug/wiki/tips#automatic-installation

let data_dir = has('nvim') ? stdpath('data') . '/site' : '~/.vim'
if empty(glob(data_dir . '/autoload/plug.vim'))
  silent execute '!curl -fLo '.data_dir.'/autoload/plug.vim --create-dirs  https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
  autocmd VimEnter * PlugInstall --sync | source $MYVIMRC
endif

" List of plugins (plugin names are github repo names?)
call plug#begin()
Plug 'sheerun/vim-polyglot'
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
Plug 'junegunn/fzf.vim'
Plug 'tpope/vim-fugitive'
call plug#end()

" Add the Ctrl + Y shortcut to copy a filename from the Files window.
" This is based on https://github.com/junegunn/fzf.vim/issues/772#issuecomment-467283202
let g:fzf_action = {
      \ 'ctrl-t': 'tab split',
      \ 'ctrl-x': 'split',
      \ 'ctrl-v': 'vsplit',
      \ 'ctrl-y': {lines -> setreg('+', join(lines, "\n"))}}

" Map \f to the fzf Files command and \b to Buffers
" The ! after the command opens the fzf window full screen
nnoremap <silent> <Leader>f :Files!<CR>
nnoremap <silent> <Leader>g :GFiles!<CR>
nnoremap <silent> <Leader>b :Buffers!<CR>

```

## Installing and configuring tmux

Run `sudo apt update` and `sudo apt install tmux` to install tmux.

Edit the file ~/.tmux.conf and paste the following contents in it.

```
# These two lines are required for vim to interpret arrow keys correctly (https://stackoverflow.com/a/38134665)
# Not sure if this breaks something else, because the tmux documentation says that terminal should be set to screen or tmux.
# Try to use this answer (https://superuser.com/a/402084) if something breaks
set -g default-terminal "xterm-256color"
set-window-option -g xterm-keys on

# Enable mouse support
set -g mouse on

# Reduce the delay after pressing Esc
set -sg escape-time 10

# Change the prefix from Ctrl + B to Ctrl + A
unbind C-b
set -g prefix C-a

# Use vi-like key bindings in copy mode
set-window-option -g mode-keys vi

# Ctrl + A followed by y will enable copy mode and move to the line above the prompt
bind y copy-mode -e\; send-keys -X cursor-up

# Allow moving between panes with the hjkl keys too (not just arrow keys)
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Fixups for Home, End, as these keys don't work correctly without this binding
bind -n Home send-keys Escape "OH"
bind -n End send-keys Escape "OF"
```

See [Using tmux](_posts/2022-11-07-working-with-tmux.md) for details on working with tmux

## Installing fzf

`sudo apt install fzf` can be used to install the latest version of the package. As of Ubuntu 24.04.1, version 0.44.1 of fzf gets installed, but this version needs some manual fixups to work correctly.

Place the following lines at the bottom of ~/.bashrc and follow the instructions in it.

```
# enable fzf key bindings
source /usr/share/doc/fzf/examples/key-bindings.bash

# The documentation mentions sourcing this file but it does not exist in the package as of 0.44.1! 
# Download this file manually from the releases page on github (https://github.com/junegunn/fzf/releases/tag/0.44.1). 
# Use the file from the release that matches the version of the package that got installed.
source /usr/share/doc/fzf/examples/completion.bash
```

The vim plugin for fzf is installed by the Plug plugin manager in vim based on the ~/.vimrc shown in an earlier section.


