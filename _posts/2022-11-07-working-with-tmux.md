---
layout: post
title:  "Working with tmux"
date:   2022-11-07 19:46:00 +0530
categories: basics
tags: 
---

## What is tmux?

tmux is a Terminal Multiplexer.

When working on remote machines with a ssh session, drops in connectivity cause the shell and any command or program it is running to get killed. Launching tmux on the remote machine, and running any other commands or programs within a tmux session ensures that the programs keep running. When connectivity is restored, the previously running tmux session can be reattached.

tmux supports multiple sessions, multiple windows in each session, multiple panes in each window, etc. These features make it possible to run multiple commands simultaneously within a single ssh session.

tmux also has a copy mode where the text on the terminal can be navigated with the keyboard using vim or emacs like key bindings.

## Installing tmux

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

# Ctrl + A followed by k will enable copy mode and move to the line above the prompt
bind k copy-mode -e\; send-keys -X cursor-up

# For some reason Home and End keys don't work correctly without this binding
# https://stackoverflow.com/a/55616731
# This is probably related to the xterm-256 we have used for TERM above
bind -n Home send-keys Escape "OH"
bind -n End send-keys Escape "OF"
```

# Using tmux and working with sessions

Run `tmux` or `tmux new` or `tmux new-session` to start a tmux session. The default shortcut for sending commands to tmux is `Ctrl + B` but the configuration above changes it to the more convenient `Ctrl + A`. Keep this in mind when referring to documentation on tmux. A new session created like this gets a numeric name.

`tmux new -As name` attaches an existing session with name or creates a new explicitly named session if it does not exist. Remove the `A` flag to always create a new session. If the name already exists, tmux returns without creating a new session.

Use `Ctrl + A` followed by `d` to detach the current session. Use `D` instead of `d` to select from a list of multiple sessions. The session keeps running in the background and can be reattached later.

`tmux ls` run from outside a session shows the list of all sessions. The session name is shown at the beginning of each line.

`tmux a` or `tmux attach-session` run from outside a session attaches the most recently active session.

`tmux a -t name` can be used to attach a specific session.

From within a session `Ctrl + A` followed by `$` can be used to rename the session so that it can be identified by a meaningful name instead of a number. From outside a session `tmux rename-session -t old_name new_name` can be used to rename a session.

To run a command that would normally be run from outside a session, from within a session itself, use `Ctrl + A` followed by `:` to enter a command mode. Type the command without the `tmux` since we are already within tmux. For example to switch to a different session from within one session, press `Ctrl + A` then `:`, type `a -t name`, and then `Enter`. Some of the commands run in this way may show an output, for example running `ls` shows the list of sessions. This output can be exited by pressing `q`.

Sessions can also be switched with shortcuts:

`Ctrl + A` followed by `(` for previous session
`Ctrl + A` followed by `)` for next session
`Ctrl + A` followed by `L` for last session

# Shortcuts to manage panes

`Ctrl + A`, `%` splits panes vertically. `Ctrl + A`, `"` splits panes horizontally.
`Ctrl + A` followed by arrow keys moves into the pane in the direction of the arrow key.
`Ctrl + D` closes the pane (This is the same as exiting the shell in the pane).

`Ctrl + A` followed by `Ctrl + arrow` resizes the pane for the arrow key in an appropriate direction. Unfortunately it does not allow keeping the arrow key pressed. Using `Alt` instead of `Ctrl` along with the arrow resizes in steps of 5 instead of 1 "cell".

`Ctrl + A` followed by `Alt + <N>` where N is a number from 1 to 5 puts panes into one of the predefined layouts. 1 is vertical, 2 is horizontal.

`Ctrl + A` followed by `Ctrl + O` rotates panes forwards. `Alt + O` rotates them backwards. When there are only two, this is the same as swapping them.

# Working with the copy mode

The entire text buffer in the terminal can be navigated with the keyboard by enabling copy mode.

`Ctrl + A` followed by `[` is the standard short cut to enter copy mode. The configuration above enables entering copy mode with `Ctrl + A` followed by `k`. It also moves the cursor one line above for convenience.

Copy mode can be exited with `q`.

The vim keys hjkl as well as arrow keys can be used to move within the buffer. Upper case `J` and `K` (j and k with Shift) can be used to move and scroll simultaneously. Using `J` to get back to the prompt line also has the effect of exiting copy mode automatically.

Use the space key to begin visual selection of text and vim like motion keys to extend the selection. Use Enter to end the selection, copy it, and exit out of copy mode. Use `Ctrl + A` followed by `]` to paste the selection. This is the native shortcut for tmux. Alternatively, use `Ctrl + Shift + V` if the terminal supports it.

Use `?` to search upwards and `/` to search downwards. Use `n` and `N` to move between matches.

# Other functionality

tmux has a lot of commands and functionality not described on this page. [This is a good reference for tmux](https://man.openbsd.org/tmux)