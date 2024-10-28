---
layout: post
title:  "Glob Patterns"
date:   2022-01-18 23:30:00 +0530
categories: basics
tags: 
---

Globs are patterns that can be used to match file or directory paths. Several programs can take globs as inputs to be used as filter criteria.

When globs appear on the shell without quotes, the shell expands each glob into a list (space separated) of files or directories that match the glob. Importantly, globs that do not match anything are left as is - this is likely to cause unexpected problems. Also, if an unquoted variable on a shell command expands to a glob, the shell attempts to expands that glob too - this is also unlikely to be the desired behavior.

Full documentation about glob patterns and globbing can be found [here](https://manpages.ubuntu.com/manpages/xenial/man7/glob.7.html)

A glob has multiple pattern components separated by slashes. Each component is used to match a corresponding component of a path. Components can contain combinations of:  
`?`: matches any single character  
`*`: matches zero or more characters  
character classes enclosed in square brackets (just like the character classes in regular expressions except that the character for negation is `!` instead of `^`)
any other characters

A dot that is the first character of a path component must be matched explicitly by a dot as the first character of a glob component. `?`, `*` or any character class cannot be used to match a starting dot. Path components that start with a dot are considered "hidden" by convention, and this rule is in accordance with that convention. It does mean that a single glob cannot match both hidden and regular paths.

## Examples

`?est` matches the path `test`, `best`, but not `.est/`.
`?est/` matches the directory paths `test/`, `best/`, but not `.est/`.
`test/*.1` matches `test/f.1`, `test/fg.1`, but (perhaps surprisingly) not `test/.1`

