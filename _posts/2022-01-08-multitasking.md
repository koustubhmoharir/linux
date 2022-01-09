---
layout: post
title:  "Multitasking"
date:   2022-01-08 21:00:00 +0530
categories: basics
tags: 
---

`Ctrl + Z` suspends the currently running program. For example, if the currently running program is the vim text editor launched from the terminal, Ctrl + Z returns to the terminal immediately placing the editor in a suspended (stopped) mode. The job id of this suspended program is shown in the output within square brackets

Multiple programs can be in a suspended state.

`fg` brings the last suspended program to the foreground.

`fg %job_id` brings the program with the specified id to the foreground.

`jobs -l` lists all the current jobs with their job ids, process ids, status, and commands. This output can be used as input to `fg %job_id` to resume the process by job id, or to `kill pid` to kill the process by process id.

There is also a `bg %job_id` command to let the program continue running in the background. To start a program in the background, ` &` can be placed at the end of the command. Programs running in the background can write output to the shell. To prevent this from happening, the standard output and standard error may be redirected.

`disown -h %job_id` can be used to keep a program running even when the shell exits. Any output written by the program to the shell after the shell exits will be lost.
