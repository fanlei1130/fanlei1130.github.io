---
layout:     post
title:      "Spark 安装教程"
subtitle:   ""
date:       2019-01-03 23:09:00
author:     "Shitao"
header-img: ""
catalog:    true
tags:
    - Spark
    - 安装教程
    - 计算引擎
    - 大数据
    - Apache
---


# Spark 安装教程

## 安装环境

- Fedora 29
- openjdk version "1.8.0_191"

## 安装步骤

1. 下载 `Spark` 安装包
2. 解压 `Spark` 安装包 `/opt/spark` 目录
3. 修改 `Spark` 配置
4. 修改 `PySpark` 配置

### 下载 `Spark` 安装包

下载地址：`http://spark.apache.org/downloads.html`

### 解压 `Spark` 安装包 `/opt/spark` 目录

##### 创建 `/opt/spark` 目录

```bash
sudo mkdir -p /opt/spark
```

##### 解压到  `/opt/spark` 目录

```bash
cd /opt
sudo tar -zxvf ~/Downloads/spark-2.3.0-bin-hadoop2.7.tgz
sudo mv /opt/spark-2.3.0-bin-hadoop2.7 /opt/spark
```

### 修改 `Spark` 配置

#### 修改 `Spark` 环境变量

```bash
sudo vim ~/.bashrc
```

> 修改内容

```text
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export SPARK_HOME=/opt/spark
export PATH=$SPARK_HOME/bin:$PATH
```

##### 使环境变量生效

```bash
source ~/.bashrc
```


### 安装 `PySpark`

```bash
pip3 install pyspark
```

### 修改 `PySpark` 配置

##### 修改 `PySpark` 环境变量

```bash
sudo vim ~/.bashrc
```

> 修改内容

```text
export PYSPARK_PYTHON=python3
```

##### 使环境变量生效

```bash
source ~/.bashrc
```
