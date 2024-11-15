---
layout: post
title:  "Changing owners and permissions (chmod, chown, chgrp)"
date:   2024-11-09 22:28:00 +0530
categories: basics
tags: 
---

## chmod

The [Documentation on chmod](https://manpages.ubuntu.com/manpages/focal/en/man1/chmod.1.html) does not provide any examples or recommedations and is difficult to read.

The "mode" in chmod can be specified with symbols (r, w, x, etc) at a granular level or with octal numbers to set the complete permissions.

### Examples of numeric usage

The numeric mode is simple because it directly sets the complete permissions. This mode should be preferred when the full permissions to be set are known. This mode should **not be used** when setting permissions on a directory recursively with `-R`, because the interpretation of 'execute' permissions on files and directories is different. 

`chmod 754 target` sets the permissions as `rwxr-xr--`. setuid and setgid are cleared, i.e 754 is the same as 0754.

`chmod 4754 target` sets `rwsr-xr--`.

**Important notes**:  

A user who does not belong to the owning group of a file cannot set the setgid bit.

If the target is a directory, `chmod 444 dir` or `chmod 0444 dir` **does not** clear the setgid bit. To clear this bit, an additional leading 0 is required, i.e, `chmod 00444 dir` is required to clear setgid. This protects against accidental clearing of group ownership inheritance on directories. Similar protection **does not apply** to the sticky bit for restricted deletion.

### Examples of symbolic usage

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

### Recursive flag

The executable bit on a file makes the file executable, but most files are not executable. On the other hand, the executable bit on a directory makes it searchable, and most directories should be searchable. Hence chmod has a special flag `X` instead of `x` for the permission. When `X` is provided, chmod applies `x` only if it is already set for at least one of owning user, owning group, or others. Assuming that executable files have already been marked as executable, `X` can be used in recursive mode for the common case of making directories searchable without making all files executable.

`chmod -R --quiet ug=rwX,o=rX dir` applies the operation recursively. For files that are already executable (already have x somewhere in their existing permissions) and for directories, the permissions are set to rwx for user and group, and rx for others. For files that are not already executable, the permissions are set to rw for user and group and r for others.

### Symbolic links

chmod does not change permissions of links since they are not used. If a link is provided directly as an argument to chmod, chmod changes the permissions on the target of the link. However, if a link is encountered during traversal with the -R option, the permissions of the target are not changed.

## chown

When a file is created, the user who created it becomes the owning user. If the directory in which the file is created has the setgid bit set, the owning group of the directory becomes the owning group of the file, else the primary group of the user creating the file becomes the owning group. A root user (or another user with the necessary capabilities) can call chown to change just the owning user or both owning user and owning group. **Hence it is usually necessary to prefix a chown command with sudo**.

All of the complexity in chown comes from specifying the handling of symbolic links. The basic usage is simple:

`chown user target` changes the owning user of target to user  
`chown user:group target` changes the owning user of target to user and owning group to group  
user and group can be either names or numeric ids. If a number is provided, it will be interpreted as a number only if it cannot be resolved to a name. To explicitly specify a numeric id, prefix the number with `+` as in `chown +1000 target`.

If target is a symbolic link, the owner of the file or directory pointed to by the link is changed, not the owner of the link itself. The owner of a link is important only when the link is in a directory with the restricted deletion bit set (represented by t).

`chown -h user target` sets the owner of the link instead of whatever the link points to.

When target is a directory, the `-R` option can be used to change the owner recursively. One of the mutually exclusive options `-H`, `-L`, `-P` should be specified along with `-R` to control whether chown acts on links or actual files or directories.

`-L` follows all links and sets owner on actual files or directories.  
`-P` does not follow any link and sets owner on the links.  
`-H` follows only direct links, i.e, the links provided as arguments to chown. Any further links encountered during recursive directory traversal are not followed. In other words, `-H` follows top level links but acts like `-h` on all links encountered during traversal.

**If none of these three options is specified, the behavior is undefined**.

`chown -RL user dir` changes owner recursively on dir and follows all symbolic links.

L may be remembered as logical (acts on the meaning of the file by following links), and P as physical (acts on the file as it exists on the filesystem, not caring about whether it is a link).

## chgrp

chgrp is very much like chown. It has the same `-h`, `-R`, `-H`, `-L`, `-P` options with the same meaning.

`chown group target` changes the owning group on target to group. If target is a link, the link is followed.

`chown -RL group dir` changes owner recursively on dir and follows all symbolic links

Instead of specifying the group, chgrp can also be used with `--reference` to copy the owning group from another file

`chown --reference source target` copies the owning group from source and applies it to target.

