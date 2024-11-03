---
layout: post
title:  "Installing Docker on Ubuntu on WSL"
date:   2024-10-28 16:00:00 +0530
categories: notes
tags: 
---

The [official Docker installation page for Ubuntu](https://docs.docker.com/engine/install/ubuntu/) has instructions for installation of Docker Engine that can be adapted for use with WSL. The official page promotes the installation of Docker Desktop which bundles Docker Engine and requires a commercial license for most organizations. Docker Engine can be used without Docker Desktop.

## Verify systemd is enabled in WSL

Recent versions of WSL use systemd by default for Ubuntu, but older versions don't. If you installed Ubuntu before this change, systemd may not be enabled. Run `wsl --update` in cmd to ensure that wsl is up to date.

Open a terminal on Ubuntu and open the file `/etc/wsl.conf` in a text editor (use `nano /etc/wsl.conf` to open the file in nano). If you see `systemd=true` in the boot section, systemd is already enabled, skip the rest of this section.

If not, add this line in the boot section as shown below and save the file.

```
[boot]
systemd=true
```

## Configure WSL networkingMode to mirrored

Create or open the file `%UserProfile%\.wslconfig` in Windows and set the networkingMode in wsl2 section to mirrored as shown below.

```
[wsl2]
networkingMode=mirrored
```

The default networkingMode is NAT. Changing it to mirrored enables applications running in WSL to connect to applications running in Windows with 127.0.0.1 as localhost. [The Microsoft page on networking considerations in WSL](https://learn.microsoft.com/en-us/windows/wsl/networking?source=recommendations) has more details.

If you made any changes to WSL configuration in this or the previous section, exit out of Ubuntu with `exit` and then run `wsl --shutdown` in cmd. Then open Ubuntu again.

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

## Start the Docker daemon if necessary

Run `systemctl is-active docker.service` to check if the docker is running. If not, start it with `sudo systemctl docker start`
Similarly, check if docker is configured to auto-start with `systemctl is-enabled docker.service`. If not, enable it with `sudo systemctl enable docker` if desired. If it is not enabled, you will need to start it manually before running any docker commands.

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

## Configure Docker to rotate the log file

Docker's log files may eventually take up too much disk space. [Logging can be configured](https://docs.docker.com/engine/logging/drivers/json-file/) as desired. Save the configuration below to /etc/docker/daemon.json as a starting point.

```
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```
