---
layout: post
title:  "Working with files and directories"
date:   2024-11-21 01:10:00 +0530
categories: basics
tags: 
---

## Copying files

Copying files seems like a very simple operation. However there are many aspects to consider.
- copying the file content
- copying the timestamps
- copying extended attributes
- copying ownership (user and group)
- copying the file permissions
- handling of symlinks
- handling of hard links
- whether the destination name should be interpreted as a file or a directory
- should existing files be overwritten

All of this complexity and more is covered by the many options to the cp program.

The cp command has two unambiguous forms:

`cp -t directory [options] source...` copies 'into' directory one or more sources. If directory does not exist or it is a file, cp will fail.  
`cp -T [options] source destination` copies 'This' source to destination. destination will be created if it does not exist, otherwise it will be overwritten. If source is a file and destination is a directory, or vice versa, cp will fail.

To remember the order of arguments, the t in 'into' is lowercase because it is not the first letter of the word, the T in 'This' is uppercase because it is the first letter. The order then follows from the rules of English.

There is a third form `cp [options] source... destination` where the last argument is treated as the destination, but the behavior of this form of the command depends on whether the last argument has a trailing slash or not, whether it exists or not, and whether it is a file or a directory if it does exist. It is best to avoid this form altogether.

If source is a directory the `-R` (recursive) option is necessary because cp does not copy directories unless this option is specified.

Without specific options to copy, the timestamps on the copied files will reflect the time of copying and not the time of modification of the original file. The owner of the new files will be the user doing the copy, and the owning group will be the primary group of the owning user or the owning group of the parent directory if the mode on the parent directory is set to inherit owning group. The mode (permissions) on the new files will be the mode of the source files with setuid, setgid, and sticky bits cleared, and umask applied. Thus the permissions on the copied file will be at least as restrictive as the source file, and with the default value of umask, the owning group and others will not have write permission on the file.

If a source is a symlink, the link is "followed" before the copy. The target of the symlink is copied, but the name is the name of the symlink, not its target. `-P` can be used to copy the symlink as is (Physically).

If destination is a symlink to an existing file, the link is followed and the target of the link is overwritten. There is no option to change this behavior. If the destination is a broken symlink, cp returns an error instead of creating a missing file.

If a directory with the same name already exists, the contents of the source directory are copied into the existing directory. If a file with the same name already exists, it is overwritten without any error. The `-i` option can be used to interactively prompt for confirmation to overwrite. Alternatively, `-u none` can be used to specify the update rule as 'none' (do not update any existing files). `-u older` or just `-u` updates existing files only if they are older than the source files.

The `-b` option makes a backup of any file that would be overwritten with a `~` as a suffix by default.

The options specified above should generally be sufficient for most interactive use. Some of the advanced but still relatively frequently needed options are listed below. For full details, use `cp --help`.

Just like chown, cp supports the `-L`, `-P`, `-H` options with the same meaning. When used in combination with `-R`, `-L` follows all symlinks and copies targets, `-P` does not follow any symlinks and copies the links as is, `-H` follows symlinks only when they are direct arguments to cp, not the links encountered during recursive traversal.

The `-p` option (for preserve) can be provided with a comma separated list of attributes to copy from the source. The valid attributes are: mode, ownership, timestamps, links, context, xattr, all. If just `-p` is provided, it is equivalent to `-p mode,ownership,timestamps`. With `-p links` If there are multiple files in the source that are hardlinks to the same inode, the corresponding copies will also be hardlinks to the copy of the source inode.

The `-a` option is useful for copying files to an archive. It is equivalent to `-R -P -p all`. In other words, it operates recursively, copies symlinks as symlinks, and copies all attributes (in particular, it maintains hardlinks).
