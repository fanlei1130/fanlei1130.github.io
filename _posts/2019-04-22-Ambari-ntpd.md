---
layout:     post
title:      "Ambari 集群时间同步配置教程"
subtitle:   ""
date:       2019-04-22 21:20:00
author:     "Sitoi"
header-img: ""
catalog:    true
tags:
    - Ambari
    - ntpd
    - 教程
---

# Ambari 集群时间同步配置教程

## 步骤

- 在时间同步主节点创建 `ntp.conf` 文件
- 在时间同步从节点上创建 `ntp.conf` 文件
- 修改所有节点时区
- 重启主节点 `ntpd` 服务
- 将所有从节点时间与主节点时间同步
- 检测时间是否同步
- 重启从节点 `ntpd` 服务

### 在时间同步主节点创建 ntp.conf 文件 

选择一台设备当时间同步主节点，运行以下命令，编写文件 `ntp.conf`

```bash
sudo vim /etc/ntp.conf
```

写入如下内容：

```text
driftfile /var/lib/ntp/drift

restrict default nomodify notrap nopeer noquery

restrict 127.0.0.1 
restrict ::1

# restrict 网段 mask 子网掩码
restrict 192.168.1.0 mask 255.255.255.0    # 修改这行     

server 127.127.1.0

includefile /etc/ntp/crypto/pw

keys /etc/ntp/keys

disable monitor
```

### 在时间同步从节点上创建 ntp.conf 文件 

除主节点外的所有设备，运行以下命令，，编写文件 `ntp.conf`

```bash
sudo vim /etc/ntp.conf
```

写入如下内容：

```text
driftfile /var/lib/ntp/drift

restrict default nomodify notrap nopeer noquery

restrict 127.0.0.1 
restrict ::1
# server 从节点 IP 地址
server 192.168.1.152       # 根据从节点实际 IP 地址修改

includefile /etc/ntp/crypto/pw

keys /etc/ntp/keys

disable monitor
```

### 修改所有节点时区

在`每一个`节点上运行以下命令将所有节点的时区修改为 `Asia/Shanghai`

```bash
timedatectl set-timezone Asia/Shanghai
```

### 重启主节点 ntpd 服务

在主节点运行以下命令：

```bash
systemctl restart ntpd
```

### 将所有从节点时间与主节点时间同步

在每台从节点机器上运行如下命令：


> 其中：192.168.1.151 表示主节点 IP 地址

```bash
ntpdate 192.168.1.151
``` 

### 检测时间是否同步

检测每台设备时间是否一致，运行如下命令：

```bash
$ date

Thu Apr 18 16:50:07 CST 2019
```

### 重启从节点 ntpd 服务

在每一个从节点运行以下命令：

```bash
systemctl restart ntpd
```

> Ambari 集群时间同步成功