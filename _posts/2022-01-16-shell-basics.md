---
layout: post
title:  "Shell Basics"
date:   2022-01-16 22:34:00 +0530
categories: basics
tags: 
---

Documentation on the dash shell (default in Ubuntu for non-interactive use) can be found [here](https://manpages.ubuntu.com/manpages/xenial/man1/sh.1.html)

## Command syntax

The first word of a simple command (single command without redirection) is typically the path to an executable or the name of a builtin. This may be followed by arguments in subsequent words separated by spaces. For executables that are on the search path (determined by the PATH environment variable), only the name of the executable needs to be specified

<aside>There are some names like `echo` that are both a shell builtin as well as an executable and there may be differences in behavior between them. In such a case, the shell will interpret `echo` as a builtin. To force the shell to interpret a name as an executable, start the command with `command`. For example `command echo 'whatever'` will run the executable echo instead of the builtin.</aside>

Example: `ls -phlA` references an executable called ls.  
`which ls` can be used to show the full path where ls is located.
`-phlA` is an argument. Most executables and builtins interpret an argument with a starting hyphen as a combination of single letter options. In this case the `p`, `h`, `l`, `A` options are all specified. The order in which these are specified does not matter. There may be a long-form version of single letter options. For example `ls -phlA` is equivalent to `ls -phl --almost-all` where the `A` option is specified in long-form.

Example: `cd /` references a builtin called cd.  
`which cd` outputs nothing because there is no executable called cd on the PATH.  
`type cd` outputs "cd is a shell builtin"

`help builtin_name` can be used to get help on the usage of a builtin.

## Variables

Variables can be created with the syntax `NAME=value`. Spaces on either side of the assignment are not allowed. Variable names can only contain letters, numbers, and underscore, and must not start with a number. Variables created like this are *local* variables.

A variable can be exported with `export NAME` or `export NAME=value` to make the variable an *environment* variable.

Local variables are not accessible in any child process of the shell. Environment variables are copied into the environment of child processes.

## Strings

Arguments or values that do not contain a space or any other character that is interpreted specially by the shell can be written directly. If such characters exist, some quoting or escaping mechanism needs to be used. For example `echo $TERM` interprets `$TERM` as a reference to a variable and prints the value of the variable. However `echo '$TERM'` prints the string $TERM literally without interpreting $ as a variable. `echo \$TERM` also does the same thing because the `$` is escaped with `\`.

Double quotes can be used to allow string interpolation. For example in `echo "TERM is $TERM"`, `$TERM` is replaced by its value. This can also be written as `echo "TERM is ${TERM}"`. This form is useful when the character immediately after the variable name is a letter, number, or underscore.

When variables are used as arguments, they should generally be placed in double quotes, because the shell expands them literally. Consider this example:
```
$ ABC='a b c'
$ echo $ABC
```
The `echo $ABC` command is expanded by the shell to `echo a b c` (3 separate arguments to echo) and not to `echo 'a b c'` (1 argument to echo). This is usually not desirable. `echo "$ABC"` is equivalent to `echo 'a b c'`.

## Variable expansion

When the shell replaces an expression of the form `${VAR}` with the value of VAR, it is called variable expansion.

It can be useful to detect whether a variable is set or not and whether it has a value or it is null. Note that an empty string is considered to be the same as null. In other words, a variable may have a non-null value, or it may have a null value, or it may not exist at all (not set), and it may be useful to determine this. This can be achieved via modifications to variable expansion.

`${VAR-word}` expands VAR if it is set, otherwise it expands *word*. This will produce a null value if VAR is null. Read `VAR-` as the condition *VAR is not set*.  
`${VAR+word}` expands *word* if VAR is set, otherwise it produces a null value. Read `VAR+` as the condition *VAR is set*.  
`${VAR:-word}` expands VAR if it is set to a non-null value, otherwise it expands *word*. Read `VAR:-` as the condition *VAR is not useful (not set or null)*.  
`${VAR:+word}` expands *word* if VAR is set to a non-null value, otherwise it produces a null value. Read `VAR:+` as the condition *VAR is useful (set and not null)*.  

The overall expression can be read as *if condition then word, else VAR* with condition read as above.

`${VAR=word}` and `${VAR:=word}`: `=` is like `-` but it also assigns the value of word to VAR when the condition is true. In other words, `${VAR:=word}` assigns the value of word to VAR if VAR is unset or null. The expansion as a whole produces the final value of VAR.

Note that word may itself be a variable, so that `${VAR:-${VAR_DEF}}` works.

## Argument parsing

<aside>Ubuntu includes ~/bin in the path automatically if it exists. Hence this is a good place to save reusable personal scripts.</aside>
It is important to understand how arguments are parsed on the commandline. Create a script called print_args in `~/bin` (This directory can be created with the command `mkdir ~/bin`) with the following content. Then logout and relogin to include the newly created bin directory in the PATH.

<aside>A for loop without an in clause loops over arguments</aside>
```
#!/bin/sh

for i
do echo "$i"
done
```

To make this script executable, run the command `chmod ug+x ~/bin/print_args`.

This script can be run to understand the parsing rules by experimentation
```
$ print_args 'a a' "b b" c
a a
b b
c
```

Strings that are next to each other without a space in between are concatenated together.
```
$ print_args 'a''b'"c"d
abcd
```

This behavior can be used to put a single quote in a string.
<aside>Note the use of the backslash to escape the single quote on either side of <code>'b b'</code></aside>
```
$ print_args 'a '\''b b'\'' c'
a 'b b' c


```

A single quote cannot be escaped inside a single quoted string. `print_args 'a \' c'` does not work as expected because the backslash is not an escape character within single quotes. Instead it is a literal character. Thus the quote after the backslash ends the quote before a and the quote after c remains open.

A simpler way to include a single quote in a string is to use double quotes around the string. However this also invokes string interpolation for any variables within the string. If this interpolation is not desired, `$` can be escaped.
```
$ print_args "a 'b b' c"
a 'b b' c
```

Within bash, **but not in dash**, putting a `$` to the left of a single quoted string allows the use of backslash as an escape character. Thus `$'a\'b'` produces the string `a'b` and `$'a\\b'` produces the string `a\b`. Because this does not work in dash, this construct is best avoided in shell scripts. In general, writing robust shell scripts is very difficult. For any non-trivial task, it is better to use a language like Python instead of Shell. Using the non-portable improvements in any particular shell variant is probably not worth it. The use of Shell can be limited to interactive use and to tie together scripts and executables written in other languages. Some information about portability of syntax between bash and dash can be found [here](https://wiki.ubuntu.com/DashAsBinSh)
