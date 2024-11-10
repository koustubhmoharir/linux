---
layout: post
title:  "chmod examples"
date:   2024-11-09 22:28:00 +0530
categories: basics
tags: 
---

The [Documentation on chmod](https://manpages.ubuntu.com/manpages/focal/en/man1/chmod.1.html) does not provide any examples or recommedations and is difficult to read.

The "mode" in chmod can be specified with symbols (r, w, x, etc) at a granular level or with octal numbers to set the complete permissions.

## Examples of numeric usage

The numeric mode is simple because it directly sets the complete permissions. This mode should be preferred when the full permissions to be set are known. This mode should **not be used** when setting permissions on a directory recursively with `-R`, because the interpretation of 'execute' permissions on files and directories is different. 

`chmod 754 target` sets the permissions as `rwxr-xr--`. setuid and setgid are cleared, i.e 754 is the same as 0754.

`chmod 4754 target` sets `rwsr-xr--`.

**Important notes**:  

A user who does not belong to the owning group of a file cannot set the setgid bit.

If the target is a directory, `chmod 444 dir` or `chmod 0444 dir` **does not** clear the setgid bit. To clear this bit, an additional leading 0 is required, i.e, `chmod 00444 dir` is required to clear setgid. This protects against accidental clearing of group ownership inheritance on directories. Similar protection **does not apply** to the sticky bit for restricted deletion.

## Examples of symbolic usage

The symbolic mode has a lot of complex behavior. It should only be used when it is necessary to modify one or more flags without affecting the other flags. The examples below show a few cases where using the symbolic mode may be useful.

`chmod ug+rx file` adds the read and execute permissions for the owning user and owning group. The write permission is not affected.

`chmod a+x file` makes file execuable for owning user, owning group, others, i.e, `a` is a shortcut for `ugo`

`chmod o-w file` removes the write permission for others.

`chmod ug+x,o-w file` is equivalent to `chmod ug+x file` followed by `chmod o-w file`.

`chmod g=u file` sets the permissions for the owning group equal to the existing permissions for the owning user.

`chmod --reference=file1 file2` copies the full permissions (including setuid, setgid, sticky) from file1 to file2.

**Important notes**:  
If the target is a directory, the setgid bit is preserved unless it is explicitly set or cleared with `s`. Here are some examples of this behavior
If the original permission was rwxr-sr-x, `chmod g-x dir` changes it to rwxr-Sr-x. Subsequently, `chmod g=rx dir` changes it again to rwxr-sr-x. To remove the setgid bit, it is necessary to explictly clear it with `chmod g-s dir`.

## Recursive flag

The executable bit on a file makes the file executable, but most files are not executable. On the other hand, the executable bit on a directory makes it searchable, and most directories should be searchable. Hence chmod has a special flag `X` instead of `x` for the permission. When `X` is provided, chmod applies `x` only if it is already set for at least one of owning user, owning group, or others. Assuming that executable files have already been marked as executable, `X` can be used in recursive mode for the common case of making directories searchable without making all files executable.

`chmod -R --quiet ug=rwX,o=rX dir` applies the operation recursively. For files that are already executable (already have x somewhere in their existing permissions) and for directories, the permissions are set to rwx for user and group, and rx for others. For files that are not already executable, the permissions are set to rw for user and group and r for others.
