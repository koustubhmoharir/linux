---
layout: post
title:  "Users and Groups"
date:   2022-01-06 18:31:37 +0530
categories: basics
tags: users
---
## Users
<aside>The /etc folder mostly contains configuration files. It may be remembered as Editable Text Configuration.</aside>
Users are maintained in the /etc/passwd file.  

<aside>getent gets entries from named databases.</aside>
The list can be obtained by running the command `getent passwd`. This returns a list of colon separated lines with the following information: username, encrypted password (x means it is stored elsewhere), user id (UID), group id (GID), full name of the user, home directory, login shell.
```
$ getent passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
...
pollinate:x:111:1::/var/cache/pollinate:/bin/false
koustubh:x:1000:1000:,,,:/home/koustubh:/bin/bash
```
In the listing above all the entries except the last line represent "system" users that were automatically created in a fresh installation of Ubuntu. The last line is the first normal user.

UID for the root user is 0.

UID for normal users starts from 1000 (This value is configurable in /etc/login.defs).

The UID is the identifier for the user. username can be changed.

## Groups
A group is a collection of users. Nesting is not supported so a group cannot include other groups. Every user is a member of exactly one primary or login group. Often the name of the group is the same as the username. Groups are identified by a group id (GID). A user may be a member of 0 or more secondary or supplemental groups.
The primary group id for each user is listed in the passwd file as shown above. The primary group can be changed with the `usermod -g group_name user_name` command.

<aside>The cat command writes (concatenates) the content of the /etc/group file to the standard output.</aside>  
The full list of group memberships can be obtained by running the command `cat /etc/group`  
The lines in this output are colon separated with the following information: group name, encrypted password (generally not used, can be used to implement privileged groups), group id (GID), comma separated list of user names who are secondary members of the group. Primary membership is not listed.
```
$ cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,koustubh
tty:x:5:syslog
...
sudo:x:27:koustubh
...
koustubh:x:1000:
```

The `groups` command outputs a space separated line of groups for the current user including the primary group. The primary group may not be the first in the list.
```
$ groups
koustubh adm dialout cdrom floppy sudo audio dip video plugdev netdev
```

The command can also be used as `groups koustubh` to get the groups for a specific user. The format of the output is different
```
$ groups koustubh
koustubh : koustubh adm dialout cdrom floppy sudo audio dip video plugdev netdev
```

Membership in the sudo group allows a user to run a sudo command.

A description of common groups can be found [here](https://wiki.debian.org/SystemGroups)