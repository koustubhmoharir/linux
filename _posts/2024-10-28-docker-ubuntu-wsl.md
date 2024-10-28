---
layout: post
title:  "Installing Docker on Ubuntu on WSL"
date:   2024-10-28 16:00:00 +0530
categories: notes
tags: 
---

The [official Docker installation page for Ubuntu](https://docs.docker.com/engine/install/ubuntu/) has instructions for installation of Docker Engine that can be adapted for use with WSL. The official page promotes the installation of Docker Desktop which bundles Docker Engine and requires a commercial license for most organizations. Docker Engine can be used without Docker Desktop.

## Uninstall conflicting packages

```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt remove $pkg; done
```
Note: This is taken from the official page but apt-get has been replaced with apt

## Setup Docker's apt repository

```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$UBUNTU_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
```
Note: This is taken from the official page but apt-get has been replaced with apt, VERSION_CODENAME has been replaced with UBUNTU_CODENAME, and the multi-line command is modified to be a single line to avoid problems with pasting.

## Install the Docker packages

```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
Note: This is taken from the official page but apt-get has been replaced with apt

## Start the Docker daemon

```
sudo service docker start
```
Note: On WSL, Docker does not start automatically at startup by default. It is possible to [configure WSL to start Docker automatically](https://stackoverflow.com/a/74662813) by editing (or creating) the file /etc/wsl.conf and making sure that the boot section in this file has the command `service docker start`. Use a text editor like nano or vi to edit this file, for example `sudo nano /etc/wsl.conf`. The space after equals is probably required.

```
[boot]
command= service docker start
```
If there is any existing command, use semi-colon to separate the commands.

Verify that Docker is running
```
sudo service docker status
```
## Test by running the hello-world image
```
sudo docker run hello-world
```

## Manage Docker as a non-root user

A group called docker is automatically created during installation. The currently logged in user can be added to this group
```
sudo usermod -aG docker $USER
```
Activate the changes to the group to avoid having to logout and login again
```
newgrp docker
```
Verify that docker commands work without sudo
```
docker run hello-world
```