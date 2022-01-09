---
layout: page
title: Guide
permalink: /guide/
---

## Getting Started

Windows users can install the Windows Subsystem for Linux (WSL) using these [instructions](https://docs.microsoft.com/en-us/windows/wsl/setup/environment) from Microsoft. As of WSL 2, this installs a real Linux kernel in a low-overhead virtual machine. The Windows filesystem is mounted under `/mnt` on Linux and the Linux filesystem is available as a network share at `\\wsl$` on Windows.

Go to Windows Terminal > Settings  
To start the Linux terminal in the Linux home directory instead of Windows profile folder, set the Starting Directory under Windows Profiles > Ubuntu to /home/username.  
To prevent Ctrl + C and Ctrl + V from interfering with Linux applications, go to Actions and delete the shortcuts. Ctrl + Shift + C and Ctrl + Shift + V will still work.


## Table of Contents

- [Users and Groups](_posts/2022-01-06-users-groups.md)
- [Filesystem](_posts/2022-01-06-filesystem.md)
- [Filesystem Permissions](_posts/2022-01-08-filesystem-permissions.md)
- [Editing Text Files](_posts/2022-01-07-editing-text-files.md)
- [Working with the console](_posts/2022-01-08-working-with-the-console.md)
- [Configure Defaults](_posts/2022-01-07-configure-defaults.md)
- [Multitasking](_posts/2022-01-08-multitasking.md)
- [Common Commands](_posts/2022-01-07-common-commands.md)

