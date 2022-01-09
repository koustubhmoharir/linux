---
layout: post
title:  "Filesystem Permissions"
date:   2022-01-08 14:34:00 +0530
categories: basics
tags: filesystem
---
## Basic permissions

The bulk of the information in this post comes from [here](https://wiki.archlinux.org/title/File_permissions_and_attributes). Some of it comes from searches and experimentation.

Every file or directory is owned by a user *and* by a group. When a new file is created by a user, usually the owner group will be the primary group of the user. However, both the owning user and owning group can be changed, and the owning user may or may not belong to the owning group, either by primary membership or secondary membership.

The basic permission model in Unix has three sets of flags - one set each for the owning user, owning group, and everyone else (others). This basic model can be extended with ACLs (Access Control Lists) for more granular control. The flags have different meanings for files and directories.

Flags can be represented as a letter or as a number which can be combined bitwise with other numbers. The number is indicated in parenthesis below. The lack of a flag is represented by `-` (0)

### Flags for files

`r` (4): If this is present, read access is granted  
`w` (2): If this is present, write access is granted  
`x` (1): If this is present, execute access is granted  

Useful and common combinations:  
`rwx` (7) - full access  
`r-x` (5) - read and execute  
`r--` (4) - read only  
`rw-` (6) - read and write but not execute  
`---` (0) - no access

Combinations that are not useful:  
`--x` (1) - execute without read. This is not useful because the file must be read if it is to be executed. However, when running as root (directly or via sudo), read permission is not required to execute. Execute permission is still required.  
`-w-` (2) - write without read. This does work but it is difficult to imagine a use-case for this.  
`-wx` (3) - combination of the cases above.

In a `ls -l` listing, the basic permissions are listed in the first column. Owning user and owning group are listed in the third and fourth columns. The value in the first column may look like `-rwxr-x---`. The `-` in the first column is not a permission flag. Rather, it indicates that this entry is a file. For a directory, it would be d. After this first character, each subsequent set of 3 characters represents the read, write, execute flags for owning user, owning group, others. The owning user has full access, the owning group has read and execute access, and others have no access. To evaluate what access a particular user has, only the first relevant set is considered and other sets are ignored. If the user is the owner of the file, the owning user set is considered, and other sets are ignored. Else, if the user belongs to the owning group (primary or secondary membership), the owning group set is considered, and other sets are ignored. Else, the others set is considered. This means that for an `ls` entry of `-rwx---rwx`, if the user is not the owner but belongs to the owning group, no access is granted, even though the others set grants full access. This is unlike Windows where all rules are combined.

The numeric representation of `rwxr-x---` is 750, formed by concatenating the numeric representation of each set.

There is one additional flag in each set. Unlike the flags above, the numerical representation is not consistent with the text representation.

For the *owning user* set, the additional flag stands for `setuid` bit and is represented in text by `s` or `S` (upper case) in the place where x would have been. When `setuid` is set, the file will be executed with the filesystem permissions of the owning user even if the current user is *not* the owning user. In other words, the executing program in the file will be allowed to do whatever the owning user is allowed to do. `s` implies `x` because it is only useful for executable files. `S` (upper case) sets `setuid` but does not imply `x` and is usually not useful. The user still needs permission to read and execute regardless of the `setuid` bit. The bit only controls *what* the program is allowed to do *when* it executes, not *whether* it is allowed to execute or not.

For the *owning group* set, the additional flag stands for `setgid` bit. This is just like `setuid` but for the owning group instead of the owning user. It is also represented in text by `s` or `S` (upper case).

For the *others* set, the additional flag stands for `sticky` bit and is represented in text by `t` or `T` (upper case) in the place where x would have been. `t` implies `x`, `T` does not. This flag has no effect on modern systems other than the implied `x`. It was used in the past for some caching optimization.

For the numeric representation of the additional flags, `setuid` bit has a value of 4, `setgid` bit has a value of 2, and `sticky` bit has a value of 1. The bitwise combination of these precedes the numeric representation of the regular flags.

`rwsr-sr-x` allows the owning user to read, write, and execute. It allows the owning group and others to read and execute. When the file is executed, it will have the permissions of the owning user and owning group regardless of who runs it.
The numeric representation of this is 6755  
6: setuid (4) + setgid (2)  
7: Owning User: read (4) + write (2) + execute (1)  
5: Owning Group: read (4) + execute (1)  
5: Other: read (4) + execute (1)

### Flags for directories

The flags and their text and numeric representations are the same for directories, but their interpretation is different.

`execute` for a directory means the ability to set it as the current working directory as well as the ability to access any files or sub-directories directly or indirectly under it. Permission to execute a directory can be thought of as permission to "enter". Without this permission, nothing within the directory can be accessed. For a file or directory to be accessible, execute permission must exist on every parent directory up to the root.

`read` for a directory means the ability to list the contents of the directory. This does not affect the permissions on files or sub-directories within it. Even without read permission on the directory, items within the directory can be read, written, or executed as long as their path is known and the permissions on those items allows these operations.

`write` for a directory means the ability to create, rename, or delete items in the directory. A file without write permission *can* be deleted or renamed if there is write permission on its immediate parent directory.

The `setuid` bit on a directory is ignored.

When the `setgid` bit is set on a directory, any newly created files or directories within it inherit the owner group. Sub-directories will also inherit the `setgid` bit. Without this bit, the owner group is the primary group of the user who created the items. Setting this flag does not affect any items already in the directory. Items moved into the directory from elsewhere also do not inherit anything.

When the `sticky` bit is set on a directory, files in that directory can only be renamed or deleted by the owner of the file or the owner of the directory. Unlike the `setgid` bit, this is not inherited by sub-directories and does not affect files in sub-directories. This bit is useful on temporary folders where multiple users may create files and files created by one user should not be deleted or renamed by another.

## Viewing permissions

The permissions for a file or directory can be viewed with the `stat` command  
`stat -c '%a %U %G' file_path` prints the numerical representation of the permissions (%a) along with owning user (%U) and group (%G) names  
`stat -c '%A %U %G' file_path` prints the text representation with file type (- for files, d for directories) as the first character (just like `ls -l`) along with owning user and group names  


## umask

umask is a process level setting that affects the permissions of newly created files and directories from that process. The default value of umask on ubuntu is 0022 or `----w--w-`. This value can be seen by running the command `umask`.  
For a newly created directory, the default permission is obtained by removing the permissions in umask from `rwxrwxrwx` or `rwxrwsrwx` (if parent directory has the `setgid` bit). In other words, the default permission for a newly created directory with the default umask is `rwxr-xr-x` or `rwxr-sr-x`.  
For a newly created file, the default permission is obtained by removing the permissions in umask from `rw-rw-rw-`. In other words, the default permission for a newly created file with the default umask is `rw-r--r--`. Linux doesn't create executable files automatically.

Confusingly, the command `umask -S` displays the bitwise *complement* of the umask in a text representation. For a numeric value of 022, `u=rwx,g=rx,o=rx` is displayed.

The `setuid`, `setgid`, and `sticky` bits in the mask (if any) are ignored.

There is no way to set umask at a directory level. It can only be set at a process level.
