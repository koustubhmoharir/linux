---
layout: post
title:  "Searching with grep"
date:   2022-01-18 23:00:00 +0530
categories: basics
tags: 
---

grep can be used to search files, directories, or the standard input for occurrences of specific text or matches of a regular expression. Full documentation can be found [here](http://manpages.ubuntu.com/manpages/focal/man1/grep.1.html)

`-F` option searches for fixed strings (not regular expressions)  
`-P` option searches for a regular expression pattern in Perl-compatible syntax (PCRE)  
The default option is `-G` which is a basic regular expression syntax. PCREs are more powerful and are very close to the .NET syntax for regular expressions.

`-i` option makes the search case insensitive  
`-w` matches full words where letters, digits, and underscore are considered word characters  
`-x` matches a full line
`-v` inverts (negates) the match

`-m num` restricts the search to a maximum of `num` matches.

`-r` reads files recursively from input directories
