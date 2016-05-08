---
title: What is Docker?
date: 2016-01-01 19:43:48
tags: Docker
---

## What is Docker

Docker allows you to package an applications with all of its dependencies into a standardized unit for development.

对于软件开发,Docker允许你用其所有相关的依赖打包应用程序到一个标准化单元中.

![layered_filesystems_sm](http://7xpk5e.com1.z0.glb.clouddn.com/docker-what_is_layered_filesystems_sm.png)

Docker containers wrap up a piece of software in a complete filesystem that contains everything it needs to run : code,runtime,system tools,system libraries - anything you can install on a sever. This guarantees that it will always run the same,regardless of the environment it is running in.

Docker容器在一个完整的文件系统中包裹一个软件,这个文件系统包含软件运行所需要的code,runtime,系统工具,系统库等任何你所能在服务器上安装的.这保证了软件总是运行在相同的环境.(这句翻译的烂,欢迎指正)

<!-- more -->

### lightweight 轻量的

Containers run on a single machine all share the same operating system kernel so they start instantly and make more efficient use of RAM. Images are constructed from layered filesystems so they can share common files, making disk usage and images download efficient.

容器运行在一个独立的机器中,共享操作系统核心,所以它可以迅速启动并且可以更有效的使用内存. 在分层文件系统中构建图片,可以共享公共文件,使磁盘的使用和图片的下载更高效.


### Open 开放的

Docker containers are based on open standards allowing containers to run on all major Linux distributions and Microsoft operating systems with support for every infrastructure.

Docker容器基于开放标准，允许容器运行在主要的Linux发行版和微软操作系统上，用来支持每一个基础设施。

### Secure 安全的

Containers isolate applications from each other and the underlying infrastructure while providing an added layer of protection for the application.

容器将每个应用程序和底层基础架构都相互隔离起来，同时为应用程序提供了额外的保存层。

## How is this different from virtual machines? 和虚拟机有什么不同

Containers have similar resource isolation and allocation benefits as virtual machines but a different architectural approach allows them to much more portable and efficient.

容器有和虚拟机类似的资源隔离和allocation benefits（分配利益？），但不同的架构方法可以让Docker容器更加便携和高效。

### Virtual Machines 虚拟机

![vm-diagram](http://7xpk5e.com1.z0.glb.clouddn.com/docker-what-is-docker-diagram.png)

Each virtual machine includes the application,the necessary binaries and libraries and an entire guest operating system - all of which may be tens of GBs in size.

每个虚拟机都包含应用程序，所需的二进制文件和库以及一个完整的客户操作系统，所有的这些都有可能是几十GB的大小。

### Containers 容器

![containers](http://7xpk5e.com1.z0.glb.clouddn.com/docker-what-is-vm-diagram.png)

Containers include the application and all of its dependencies, but share the kernel with other containers. They run as an isolated process in userspace on the host operating system. They're also not tied to any specific infrastructure - Docker containers run on any computer, on any infrastructure and in any cloud.

容器包含应用程序及其所有的依赖，同其他容器共享内核。它允许在主机操作系统上用户空间中一个独立的进程中。它不会依赖于特定的基础设施，Docker容器可以运行在任何的个人电脑上，任何的基础设施上，任何的云服务上。

## How does this help you build better software? 如何构建更好的软件

When your app is in Docker containers, you don't have to warry about setting up and maintaining different environment or different tooling for each language. Focus on creating new features, flexing issues and shipping software.

在Docker容器中运行你的app，你不必担心配置或者维护不同的环境，维护每种语言不同的工具。专心创建新的功能，修复问题以及shipping软件。

### Accelerate Developer Onboarding 加快开发

Stop wasting hours trying to setup developer environments, spin up new instances and make copies of production code to run locally. With Docker, you can easily take copies of your live environment and run on any new endpoint running Docker.

省去设置开发环境的时间，将生产代码复制到本地运行。有了Docker，你可以很容易的将生产环境的副本运行在任何运行着Docker容器的终端上。

### Empower Developer Creativity 使开发人员有创造力

The isolation capabilities of Docker containers free developers from the worries of using “approved” language stacks and tooling. Developers can use the best language and tools for their application service without worrying about causing conflict issues.

### Eliminate Environment inconsistencies 消除环境不一致

By packaging up the application with its configs and dependencies together and shipping as a container, the application will always work as designed locally, on another machine, in test or production. No more worries about having to install the same configs into a different environment.

## Easily Share and Collaborate on Application 轻松共享和协作

Docker creates a common framework for developers and sysadmins to work together on distributed applications

### Distribute and Share content 分发和共享内容

Store,distribute and manage your Docker images in your Docker Hub with your team. Image updates,changes and history are automatically shared across your orgnization.

在团队中，使用Docker Hub存储，分发，管理你的图片。图片的更新，改变和历史都会被自动的在你的组织中共享。

### Simpley share your applications with others 简单的与他人分享你的应用程序

Ship one or many containers to others or downstream service teams without worrying about different environment dependencies creating issues with your application. Other teams can easily link to or test against your app without having to learn or worry about how it works.

## Ship More Software Faster 

Docker allows you to dynamically change your application like never before from adding new capabilities , scaling out services to quickly changing problem areas.

### Ship 7X More 

Docker users on average ship software 7X more after deploying Docker in their environment. More frequent updates provide more value to your customers faster.

### Quickly Scale 快速扩展

Docker containers spin up and down in seconds making it easy to scale an application service at any time to satisfy peak customer demand, then just as easily spin down those containers to only use the resources you need when you need it

### Easily Remediate Issues 轻松纠正问题

Docker make it easy to identify issues and isolate the problem container, quickly roll back to make the necessary changes then push the updated container into production. The isolation between containers make these changes less disruptive than traditional software models.


[What is Docker原文地址](http://www.docker.com/what-docker)

















