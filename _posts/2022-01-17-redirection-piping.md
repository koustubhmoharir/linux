---
layout: post
title:  "Redirection and Piping"
date:   2022-01-17 17:34:00 +0530
categories: basics
tags: 
---
## Redirection

Every process has streams for standard input, standard output, and standard error. When an executable is run from an interactive shell with a simple command, its standard input comes from the input to the shell which itself comes from the input to the terminal. Similarly, the output and error streams of the process go to the output and error streams of the shell, and these are interpreted and rendered by the terminal.

The file descriptors for these streams are  
0 - standard input  
1 - standard output  
2 - standard error

These streams can be redirected by placing redirections to the beginning or end of a simple command. Usually redirections are placed at the end, but it does not matter. Depending on what the redirections are doing, the order of redirections can matter. The examples below show the common use cases.

`ls something > file_path`  
This redirects the output of `ls something` to the file at file_path, overwriting it if it already exists.  
Here `ls something` is the basic command. When there is no file descriptor to the immediate left of the redirection operator `>`, as in this case, `>` is equivalent to `1>`. In other words, `>` redirects the output stream by default. `2>` redirects the error stream. No space is allowed between the file descriptor (1 or 2) and `>`. Zero or more spaces are allowed to the right of `>`

`ls something > file_path 2> error_file_path`
This contains two separate redirections. The first redirection is the same as in the previous example. The second redirection redirects the error stream to the file at error_file_path.

The `>>` operator appends instead of overwriting.

The `>&` operator works with a file descriptor on its right instead of a file path. The most common use of this is `2>&1`. This means that the error stream is redirected to the same place as the output stream at the time this redirection is applied. Spaces are allowed after `>&`, at least on bash, but the common convention is not to use spaces.

`ls something > file_path 2>&1` first redirects the output stream to a file. Then it redirects the error stream to the same location as the output stream. The overall consequence is that both outputs and errors will go into the file. The order of the two redirections is important. `ls something 2>&1 > file_path` redirects the error stream to the same location as the output stream which hasn't been redirected so far. Hence errors go to the terminal. Then the output stream is redirected to a file. However this redirection has no effect on the previous redirection.

If the shell has the `-C` (no clobber) option active, redirection to a file with `>` will fail with an error. This is useful to prevent accidental overwriting of files with a redirection. To use an overwriting redirection in this case, the `>|` operator can be used instead of `>`. This operator overrides the `-C` option to allow overwriting.

The file `/dev/null` is a special file that ignores anything written to it. This can be used to ignore the output or error or both with appropriate redirections. For example, `command > /dev/null 2>&1` redirects both the standard output and standard error to `/dev/null` ensuring that anything sent to these streams is simply discarded.

The `<` operator can be used to redirect the input stream.

```
$ echo -e '1. a\n2. b\n3. c' > ~/grep_input
$ grep c - < ~/grep_input
3. c
```
In this example, the first command writes 3 lines (1. a, 2. b, 3.c) to the ~/grep_input file. The second command searches for c in its standard input which comes from the file created earlier.

## Piping commands

Multiple simple commands can be chained together with the pipe character. The output of any command in the chain goes to the input of the next command. The output of the last command goes to the terminal by default. The error streams do not flow through the pipe by default. However, this can be changed by placing redirections in any of the command in the pipeline. These redirections apply after the output of previous command is assigned to the input of next command. Thus `command1 2>&1 | command2` sends the errors produced by command1 to the same place as the output of command1 which is the input of command2 by virtue of the pipeline being setup before the redirection.

Pipelines are very useful as they avoid the need to create intermediate temporary files to hold the output of commands.
