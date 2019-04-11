---
layout:     post
title:      "Fedora 安装 MongoDB 教程"
subtitle:   ""
date:       2019-04-01 20:04:00
author:     "Sitoi"
header-img: ""
catalog:    true
tags:
    - Linux
    - MongoDB
    - NoSQL
    - 安装教程
---


# MongoDB 安装

## 安装环境

- Fedora 29

## 安装步骤

1. 安装 `mongodb` 和 `mongodb-server`
2. 启动服务

### 安装 MongoDB 和 MongoDB-Server

```bash
sudo dnf install mongodb mongodb-server
```

### 启动服务

启动 `mongod` 服务

```bash
sudo systemctl start mongod.service
```

设置开机自动启动

```bash
sudo systemctl enable mongod.service
```