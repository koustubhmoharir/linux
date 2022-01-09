---
layout: post
title:  "Working with the console"
date:   2022-01-08 21:00:00 +0530
categories: basics
tags: 
---

## Shortcuts

Ctrl + K cuts all text after the cursor and puts it in a buffer (anything that is cut goes into the buffer)  
Ctrl + U cuts all text before the cursor  
Ctrl + Y pastes the text from the buffer  
Ctrl + W cuts the word before the cursor  
Ctrl + Del cuts the word after the cursor  
Ctrl + XX jumps to the start of the text, pressing it again after typing something at the beginning of the command gets back to the previous position

## Working with command history

The Up and Down keys can be used to move up and down through previously executed commands.

To search for a previous command press `Ctrl + R` to enter search mode. Start typing the command. Move through the results by pressing `Ctrl + R` to go further back or `Ctrl + S` to move forward (after having gone back). Hit Esc or the left / right arrow keys to get back to command mode with the text of the selected command on the console. Hit Enter to accept the command.

`!!` runs the last command  
`sudo !!` runs the last command with sudo

## Editing a complex command

`Ctrl + XE` opens an editor with the text on the console. Once editing is complete, exiting the editor with a save executes the command. To exit the editor without submitting the command, delete the text in the editor first.