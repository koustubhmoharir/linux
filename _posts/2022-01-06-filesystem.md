---
layout: post
title:  "Filesystem"
date:   2022-01-06 22:34:00 +0530
categories: basics
tags: filesystem
---
## Standard Hierarchy

There is a [Filesystem Hierarchy Standard](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html) published by the Linux Foundation.

Unsurprisingly, there are many historical reasons for the hierarchy being what it is. There have been attempts to rationalize and modernize the hierarchy based on current considerations. This post draws upon [other](https://askubuntu.com/a/135679) sources as well as observations from the actual filesystem in a fresh installation of Ubuntu 20.04. Because certain directories are required for backward compatibility, Ubuntu uses symbolic links.

## Symbolic Links

Symbolic Links or symlinks or soft links are like shortcuts in Windows. They point to a file or directory in the same or different filesystem. Symlinks are shown with a starting letter of l in the first column in the output of the `ls -l` command

```
$ ls -l
total 688
lrwxrwxrwx   1 root root      7 Apr 23  2020 bin -> usr/bin
drwxr-xr-x   2 root root   4096 Apr 23  2020 boot
...
```
The first entry is a symlink from /bin to /usr/bin because it starts with l whereas the second is an actual directory because it starts with d.

## usr

The /usr directory may be interpreted as Unix System Resources - system-wide read-only files. /bin, /lib, /sbin are just links to directories within /usr with the same names. Ubuntu has removed the distinction between /bin and /usr/bin etc. Read-only refers to normal usage. During upgrades or installation of packages, the files here will obviously be modified.

/usr/bin contains system binaries that can be used by all users. There are no sub-directories inside this. The files required in /bin are listed [here](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/ch03s04.html)

/usr/lib contains shared libraries that will be loaded by system binaries. This includes the dynamically linked C library

/usr/sbin contains system binaries required for administration, i.e, only intended to be used by the root user (superuser). There are no sub-directories inside this.

/usr/share is for read-only data files that are architecture (x86, x64, etc) independent. Programs can have their own subdirectories under this or use /usr/share/misc for single files. /usr/share/man contains manuals.

/usr/local is like /usr but for software installed by the administrator rather than the one that is considered part of the system itself. It also contains sub-directories like bin, lib, sbin, share.

## etc

The /etc directory contains configuration files that are host-specific and hence editable outside of upgrades and package installation. Host-independent files would go into /usr/share and would only be modified during installation. Binaries must not be in etc. etc can be interpreted as Editable Text Configuration. There is no standard format for configuration files.

## tmp

The /tmp directory contains temporary files. These files may not be preserved across invocations of a program and may be deleted at boot.

## var

The /var directory contains variable files such as caches, logs, and other transient / temporary files.

/var/cache contains cache files that can be safely deleted. Applications are supposed to be able to regenerate these if required. Generally files for a specific package would be under a package-specific sub-directory.

/var/log contains log files either directly under it or in sub-directories.

/var/tmp contains temporary data files that are preserved across a reboot.

## opt

Generally files in a software package installed with a package manager are spread out across /usr/bin, /usr/sbin, /usr/lib, /usr/share, /etc, /var directories. Spreading out files in this way makes it easy to ensure proper file permissions.

The /opt directory contains software packages that are more self-contained. Immediate sub-directories inside /opt must have the name of the package or publisher, and further structure within these sub-directories is left upto the package. Such packages may still use /etc/opt, /var/opt directories for configuration and variable files.

## home

The sub-directories of /home are the home directories for users. The `~` character in a path is expanded to the home directory of the logged-in user.

## mnt

Sub-directories of /mnt are the roots of "temporarily" mounted filesystems. In wsl (Windows Subsystem for Linux), the Windows drives are mounted under /mnt. For example `C:\something` in Windows is accessible at `\mnt\c\something`

Removable media like pen-drives is mounted under /media

## dev

dev stands for device. Devices such as hard disks, speakers, printers, etc are represented by special files in linux. There are character special files (example: sound card) and block special files (example: hard disk). Character devices do not require buffering where as block devices do. These types are indicated by a starting letter of c (for character) or b (for block) in the first column of the `ls -l` output. There are also other types of devices like sockets and pipes.

Terminal windows are represented by character special files like /dev/pts/0, /dev/pts/1 etc. pts stands for pseudo-teletypes. More details [here](https://www.howtogeek.com/428174/what-is-a-tty-on-linux-and-how-to-use-the-tty-command/)

stdin, stdout, stderr are symbolic links in /dev that point to /proc/self/fd/0, /proc/self/fd/1, /proc/self/fd/2 respectively. 0, 1, 2 are the file descriptors for the standard streams. These paths are themselves symbolic links to something like /dev/pts/0 indicating that the standard streams are linked to the terminal.

## proc

/proc is a pseudo-filesystem for querying process information. As such, most of the "files" in it are read-only. Details about the information that can be queried can be found [here](https://manpages.ubuntu.com/manpages/xenial/man5/proc.5.html)
For each running process, there is a sub-directory of /proc with the process id (PID). For example /proc/105 contains files providing information about the process with id 105
