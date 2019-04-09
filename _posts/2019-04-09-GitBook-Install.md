---
layout:     post
title:      "Ubuntu 安装 GitBook 教程"
subtitle:   ""
date:       2019-04-09 14:04:00
author:     "Sitoi"
header-img: ""
catalog:    true
tags:
    - Linux
    - GitBook
---

# Ubuntu 安装 GitBook 教程

## 安装 nvm

安装 nvm

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

退出终端

```bash
exit
```

## 安装 node

```bash
nvm install node
```

## 安装 GitBook

```bash
npm install -g gitbook-cli
```

## 安装 GitBook 依赖

安装 svgexport

```bash
npm install -g svgexport --unsafe-perm
```

安装 calibre

```bash
sudo apt install calibre
```