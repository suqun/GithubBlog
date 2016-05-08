---
title: docker容器的操作
date: 2016-01-30 00:06:23
tags: Docker
---

### 运行容器

```bash
docker run 运行容器
-i -t  创建交互式容器
-d 创建守护式容器
--name 为容器指定一个名称
```

```bash
docker run -it ubuntu /bin/bash
root@8abb3326b08f:/# cat /etc/issue.net
Ubuntu 14.04.3 LTS
```

<!-- more -->

### 结束容器
exit 结束容器

### 查看运行的容器

docker ps 查看运行的容器

-a 查看所有容器运行状态

docker inspect 查看容器的详细信息

### 停止容器

docker stop 停止一个守护式容器

### 删除容器

```
docker rm 删除一个停止的容器
```