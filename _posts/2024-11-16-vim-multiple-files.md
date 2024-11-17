---
layout: post
title:  "Working with multiple files in vim"
date:   2024-11-16 19:44:00 +0530
categories: basics
tags: 
---

## Buffers and windows

vim shows buffers in windows and windows can be organized in tabs (if used). When a single file is opened, there is just one window and one buffer. Additional buffers can be opened with the `:e path` command where the path is relative to the current working directory. Tab completion is available when entering the path. The fzf vim plugin can be used to open files with a fuzzy search in the current directory. Use `:Files` or `\f` (if configured [as described](_posts/2024-11-16-improving-usability.md) in this guide) to bring up the finder. By default, the buffer is opened in the same window as the earlier buffer, i.e, the earlier buffer is hidden and no longer associated with the window, the new buffer is shown in its place.

The `:e path` command can also be used with files that do not exist. A new buffer is created with the appropriate name. The file is created only when the `:w` command is run.

The `gf` command (go to file) can be used to open a file whose name is under the cursor. The `:e` or `:e!` commands can be used without any filename arguments to reload the current buffer. The `!` discards any changes.

vim can be split into multiple windows. More than one window may be attached to the same buffer. This is useful when working on large files to see different parts of the file in different windows. When there is more than one window, the filename is shown in each window. If the buffer for the file has been modified, there is a [+] indicator.

## Working with a single window

Use `Ctrl+^` to toggle between the current buffer and the buffer that was most recently active. If there are more than two buffers, the `:b` command provides tab completion on all open buffers. `:b partial_name` provides tab completion on all buffers matching the partial name. The partial name does not have to be a prefix, it can be anywhere within the name of the buffer.

If the fzf.vim plugin is installed and vim is configured as described in this guide, the `:Buffers` command or pressing `\b` brings up a list of buffers that can be searched fuzzily with a preview. The list can be navigated with the arrow keys or with `Ctrl+P` and `Ctrl+N`. Pressing Enter on a buffer opens it, and pressing Esc cancels the list without changing the buffer.

The `:saveas new_name` command can be used to save the buffer as a new file (Note that vi does not have the saveas command. It is a vim feature). new_name can be specified relative to the current working directory. While on the command line, the name of the current file can be pasted with `Ctrl+R` followed by `%`. (This reads the value of the `%` register which stores the name of the current file. Note that this works in regular insert mode too, not just on the command line). The pasted name can then be modified before the command is submitted. The open buffer and its undo history is associated with the new file, and writing it again with `:w` will affect the new file. The old file becomes the alternate file. Pressing `Ctrl+^` will toggle to it but it does not have any undo history.

The `:w new_name` can be used instead of `:saveas new_name` to write the buffer to a new file without changing its association with the old file. The undo history also remains with the old file. The new file becomes the alternate file. Pressing `Ctrl+^` will toggle to it but it does not have any undo history.

Note that neither of these commands *rename* the current file. Renaming a file can be achieved in 2 steps with a `:f new_name` command, that changes the name associated with the buffer, but *does not* create a new file. The old file can then be moved to the new file with `:!mv '#' '%'`. The # and % are registers that contain the "alternate name" and current name. The single quotes around the registers are a possibly inadequate attempt to avoid problems if there are spaces in the file name. It may be safer to just use a plugin that handles renaming instead of this.

The `:q` command quits the current window. When there is a single window, it quits vim too regardles of the number of open buffers. If any buffer has unsaved changes, the command fails and one of the unsaved buffers becomes the active buffer. This may be unexpected. The `:q!` command discards the changes in the active buffer, but *does not* discard changes in hidden buffers if any, and the command still fails. `:qa!` discards any changes in all buffers and quits. To avoid surprises and confusing behavior, it is best to use `:ls` or `\b` (from the fzf plugin) to look at all buffers. Buffers with unsaved changes are shown with a +.

The `:bd` command can be used to delete the active buffer. `:bd!` can be used to discard changes. Doing this when a file no longer needs to be worked on can make it simpler to work with multiple buffers.

## Using multiple windows

Pressing `Ctrl+W s` or the `:split` command splits the current window horizontally (the separator between windows is horizontal), so that both windows show an independent view of the same buffer. Pressing `Ctrl+W v` or the `vsplit` command splits vertically. Splitting horizontally may have been the default because wide screens were not common earlier. If the terminal supports it, the mouse can be used to drag the separator between windows to resize them.

Use `Ctrl+W` followed by the hjkl or arrow keys to move between windows. Press `Ctrl+W c` to close the current window (the last window cannot be closed but this is useful to close the split).

Any command that opens a buffer without explicitly specifying a window will do so in the current window. Note that `Ctrl+^` cannot be used to toggle between windows. It will toggle the buffer in the current window as described in the previous section.

`Ctrl+W` followed by `Ctrl+O` makes the current window the only window. Other windows are closed.

`Ctrl+W r` and `Ctrl+W R` rotate windows downwards or upwards.

`Ctrl+W x` exchanges the current window with the next or previous window.

`Ctrl+W` followed by one of the HJKL keys moves the current window to the extreme left, bottom, top, or right positions with full height / width. When there are only 2 windows, this can switch the layout between horizontal and vertical

`Ctrl+W =` makes widths / heights of all windows equal.

`Ctrl+W` followed by `[N]+` or `[N]-` where `[N]` is an optional number increases or decreases the height of the current window by N lines.

`Ctrl+W` followed by `[N]>` or `[N]<` where `[N]` is an optional number increases or decreases the width of the current window by N units.

When using fzf's `\f` or `\b` to find files or buffers, pressing `Ctrl+X` splits the window horizontally (x for horizontal) and `Ctrl+V` splits the window vertically with the buffer opened in the top or left windows created in the split.


