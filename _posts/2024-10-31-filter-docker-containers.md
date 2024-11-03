---
layout: post
title:  "Filtering Docker containers"
date:   2024-10-28 16:00:00 +0530
categories: notes
tags: 
---

## Filter all containers by image name

```
docker ps -a --filter ancestor=image_name_here
```
To get only the container ids so that they can be passed to docker rm, the -a flag can be used. For example:
```
docker rm $(docker ps -aq --filter ancestor=image_name_here)
```