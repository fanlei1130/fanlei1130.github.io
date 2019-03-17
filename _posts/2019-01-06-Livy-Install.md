---
layout:     post
title:      "Livy 安装教程"
subtitle:   ""
date:       2019-01-06 20:57:00
author:     "Sitoi"
header-img: ""
catalog:    true
tags:
    - Livy
    - 安装教程
    - REST服务
    - 大数据
    - Apache
    - Spark
---

# Livy 安装

## 安装环境：

- Fedora 29
- Spark
- PySpark

## 安装步骤：

1. 下载 `Livy` 安装包
2. 解压 `Livy` 安装包到 `/opt/livy/` 目录
3. 修改配置
4. 启动 `Livy`

### 下载 `Livy` 安装包

下载地址：`http://livy.incubator.apache.org/get-started/`

###  解压 `Livy` 安装包到 `/opt/livy/` 目录

##### 创建 `/opt/livy` 目录

```bash
sudo mkdir -p /opt/livy
```

##### 解压到  `/opt/livy` 目录

```bash
sudo unzip livy-0.5.0-incubating-bin.zip  -d /opt/
sudo mv /opt/livy-0.5.0-incubating-bin /opt/livy
```

### 修改配置

##### 将配置模板 `livy.conf.template` 复制并命名成 `livy.conf`

```bash
sudo cp /opt/livy/conf/livy.conf.template /opt/livy/conf/livy.conf
```

##### 更改配置文件

```bash
sudo vim /opt/livy/conf/livy.conf
```

> 更改内容：取消注释，加上 `/`

```text
livy.file.local-dir-whitelist = /
```

### 启动livy

```bash
sudo su
/opt/livy/bin/livy-server
```