---
layout:     post
title:      "yum localinstall 解决本地 rpm 包的依赖问题"
subtitle:   ""
date:       2019-04-08 10:04:00
author:     "Sitoi"
header-img: ""
catalog:    true
tags:
    - Linux
    - yum
    - rpm
---

# yum localinstall 解决本地 rpm 包的依赖问题


使用命令：

```bash
sudo rpm -ivh xxx.rpm
```

> 这样安装可能会出现很多依赖关系需要解决。为了能使软件安装过程中自动解决依赖关系，我们可以使用命令：

```bash
sudo yum -y localinstall xxx.rpm
```

在安装的同时自动解决有关依赖关系。