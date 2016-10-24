---
title: IntelliJ 使用说明
toc: true
date: 2016-10-18 17:38:43
tags: IntelliJ
---

### 安装

Windows下载地址：https://www.jetbrains.com/idea/download/#section=windows

版本选择Ultimate

下载的文件直接双击一路next安装即可。安装结束以后运行起来后，通过Server方式破解

![破解](http://7xpk5e.com1.z0.glb.clouddn.com/%E7%A0%B4%E8%A7%A3.png)

http://idea.iteblog.com/key.php

### 项目引入

破解以后，一路默认启动起来。先创建个Project，Intellij里面的project相当于workplace，可以先建一个空的project的，将项目代码检出到project里面（也可以将原有的项目copy到project文件夹下，如果不想copy，直接import也可以），然后在里面import module

检出代码后的效果：
![intellij-checkout](http://7xpk5e.com1.z0.glb.clouddn.com/checkout.png)

import后的效果：
![import1](http://7xpk5e.com1.z0.glb.clouddn.com/import1.png)
![import2](http://7xpk5e.com1.z0.glb.clouddn.com/import2.png)
![import3](http://7xpk5e.com1.z0.glb.clouddn.com/import3.png)
![import4](http://7xpk5e.com1.z0.glb.clouddn.com/import4.png)

### 配置

intellij的所有配置信息都在 File->Settings里面，请自行摸索。

![intellij-settings](http://7xpk5e.com1.z0.glb.clouddn.com/settings.png)

这里说说常用的几个配置在哪里。

1、项目结构

![structure1](http://7xpk5e.com1.z0.glb.clouddn.com/structure1.png)

![structure2](http://7xpk5e.com1.z0.glb.clouddn.com/structure2.png)
这里面可以配置module的语言版本，添加jdk，jar引入等


2、maven

![maven1](http://7xpk5e.com1.z0.glb.clouddn.com/maven1.png)

![maven2](http://7xpk5e.com1.z0.glb.clouddn.com/maven2.png)

3、Server

以tomcat为例

![tomcat1](http://7xpk5e.com1.z0.glb.clouddn.com/Server1.png)

![tomcat2](http://7xpk5e.com1.z0.glb.clouddn.com/Server2.png)

配置好Server的基本信息，完成这一步保存，然后添加具体的Server

![tomcat3](http://7xpk5e.com1.z0.glb.clouddn.com/Server3.png)

添加本地Server，Server标签页更改端口等配置信息

![tomcat4](http://7xpk5e.com1.z0.glb.clouddn.com/Server4.png)

Deployment里面部署war包，点击加号，选择

![tomcat5](http://7xpk5e.com1.z0.glb.clouddn.com/Server5.png)

![tomcat6](http://7xpk5e.com1.z0.glb.clouddn.com/Server6.png)

### 外观字体样式修改

通过File->Import Setting可以直接导入Intellij的配置信息。我这里有个jar包，直接导入即可

jar地址：https://pan.baidu.com/s/1kU6DxZL

导入重启即可，调整了字体大小，文件注释模板，默认UTF-8，使用了sublime类似的主题。




