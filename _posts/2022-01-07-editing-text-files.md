---
layout: post
title:  "Editing text files"
date:   2022-01-07 18:42:00 +0530
categories: basics
tags: 
---

## Searching in the buffer

Press `/` in command mode to start searching forward from the cursor. Press `?` to start searching backwards. Pressing either of these keys opens a prompt with this key on the prompt. The text to be searched can then be typed on this prompt.

The search text is always interpreted as a regular expression. Unfortunately, the default rules on which characters are interpreted literally and which ones are interpreted as a metacharacter are inconsistent with most modern regular expression flavors. To avoid remembering these rules, it is better to start the search text with either `\v` or `\V`. Starting it with `\v` tells vim to interpret all characters that can possibly be metacharacters as metacharacters unless escaped with `\`. Starting with `\V` tells vim to interpret all characters literally unless escaped with `\`. If the search text has no ambiguous characters then there is no need to use either `\v` or `\V`.

`/\v[vim]` followed by Enter searches for a single character that can be either v, i, or m.  
`/\V[vim]` followed by Enter searches for the literal string `[vim]`

Having to always type two extra characters can be a bit annoying, but the good thing about this is that regex functionality is always available with the choice of making it easier to type literal text or regex.

The v in `\v` supposedly stands for 'very magic'! It may be easier to remember it as verbatim instead, i.e, vim search works with verbatim regular expressions. To type verbatim text instead, the v is negated by making it upper case.

Pressing Enter after typing in the search pattern moves the cursor to the beginning of the first match in the direction indicated by `/` or `?`. Press `n` for the next match in the same direction or `N` for the next match in the opposite direction.

`/\v<vim>` matches only the full word vim, it does not match substrings. `/\v<vim` matches prefixes and `/\vvim>` matches suffixes.

Pressing the up or down arrow keys immediately after pressing `/` or `?` brings up past searches. Pressing backspace or delete immediately after `/` or `?` cancels the search.

To just search for the full word under the cursor, press `*` for forward search and `#` for backward search. This is obviously much faster than typing in the search text but only works if the cursor is already on the text to be searched.

vim regular expressions are case sensitive by default. To make them case insensitive `\c` can be added anywhere within the regular expression. Adding it before `\v` or `\V` or at the end of the search text is good for readability.

[Consult this excellent article on vim regular expressions](https://thevaluable.dev/regular-expression-basics-vim-grep/) for more details.

It can be difficult to spot the cursor when a search moves it. The .vimrc configuration in [Improving Usability](_posts/2024-11-16-improving-usability.md) flashes the cursor line on pressing `\c`.

## Motions

In command mode, use the `h`, `j`, `k`, `l` keys instead of the arrow keys for moving left, down, up, right respectively by one unit (character or line). This is much faster than moving the fingers to reach the arrow keys.

Typing a number before these keys moves the cursor by the specified number of units. For example, `5j` moves the cursor down by 5 lines. When combined with the feature that shows relative line numbers in the left margin, jumping to any visible line precisely is very easy. This can be particularly useful on slow connections where holding down a key is very imprecise and frustrating.

To go to a specific absolute line number, type the line number followed by either `G` or `gg`. For example `135G` takes the cursor to line number 135, or the last line if the buffer contains fewer than 135 lines. `gg` without a count before it goes to the top of the buffer and `G` without a count before it goes to the bottom.

`e` moves to the end of the current word, but if the cursor is already on the last character of a word, it moves to the end of the next word, where a word is a consecutive sequence of letters, numbers, and underscores. Similarly, `b` moves to the beginning of the current word or the previous word if it is already at the beginning of the current word. `E` and `B` (i.e `e` and `b` with Shift) can be used to shift the interpretation of word to a consecutive sequence of non-whitespace characters. `w` and `W` can be used to jump to the beginning of next word with the same interpretations of a word. `ge` and `gE` can be used to move to the end of the previous word, but this is not convenient. A lot of commands in vim can be modified in some way by prepending them with g. There does not seem to be any clear pattern to this.

Each of these keys (`e`, `b`, `w`) can also be preceded by a number to move by that many words. For example `4e` is the same as pressing `e` 4 times. This is a general pattern in vim.

`(` and `)` can be used to move to the beginning of the previous or next sentence. This is useful when working with text documents that may have many sentences on the same line. `{` jumps above the current paragraph and `}` jumps below the current paragraph.

To move to the beginning of the current line, press `0`. To move to the first non-blank character on the current line (the "real" beginning of the line), press `^`. To move to the end of the current line, press `$`. `^` and `$` are easy to remember as they mean beginning and end in regular expressions too.

`-` and `+` can be used to move to the previous or next line with the cursor at the beginning of the line. This can be useful when there is a need to paste something at the beginning of multiple lines. Like other commands, these commands can also be preceded by a number.

`f` followed by any character finds the next occurrence of the character on the current line and moves the cursor on that character. `F` finds the previous occurrenence. `t` followed by character moves the cursor on the character before the next occurrence. `T` moves the cursor on the character after the previous occurrence (same as t but in the other direction). `;` repeats the last such command. `,` repeats the last such command in the other direction. These commands can be used to quickly move to the desired position on the current line. Repeating a `t` or `T` command manually would not move the cursor at all, but when it is repeated with `;` or `,`, the effect is as if the cursor has been moved by one position between the repetions.

`[` followed by `(` or `{`,  can be used to jump to the previous opening parenthesis or brace. `]` followed by `)` or `}` can be used to jump to the next closing parenthesis or brace. This is useful in code files. Note that this does *not* work with `[[` or `]]`. These sequences jump to the previous or next section instead.

`m` followed by a letter from `a` to `z` (lower or upper case), `'`, or `` ` `` sets a mark at the current cursor position identified by the character after it, say `a`. `'a` (single quote) jumps to the beginning of the line containing the mark `a`. `` `a`` (tilde) jumps to the position. Using `'` or `` ` `` is convenient as it is easier to type the same character twice to return to the mark.


`%` jumps to the matching parenthesis, brace, bracket. Note that this does *not* work for quotes.

Pressing `Ctrl+o` and `Ctrl+i` moves the cursor to the previous and next positions in the jump list. The jump list is created when a jump command is used like the search commands, jump to mark commands, `%`, `(`, etc. Motion commands that are limited to a single line are *not* jump commands.

## Older content (to be organized properly)

Most of the content below is taken from [Beginner's guide to vim](https://www.linux.com/training-tutorials/vim-101-beginners-guide-vim/). It takes some getting used to, but it is easy to see that vim is a [powerful editor](https://www.barbarianmeetscoding.com/blog/boost-your-coding-fu-with-vscode-and-vim).



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
