---
layout:     post
title:      "利用 Docker 搭建 IPFS 私有网络"
subtitle:   ""
date:       2019-04-03 12:04:00
author:     "Sitoi"
header-img: "img/post/bg/ubuntu.jpg"
catalog:    true
tags:
    - IPFS
    - Docker
    - 教程
---


# 搭建 IPFS 私有网络

下载项目

> 项目地址：https://github.com/Sitoi/Private-Network-IPFS.git

## 步骤

- 通过 docker 创建 IPFS 容器
- 确保配置 IPFS API 以允许跨源（CORS）请求
- 移除默认的 boostrap 节点
- 重启服务

### 通过 docker 创建 IPFS 容器

运行 make 命令

```bash
make up
```

**or** 

运行 docker-compose 命令

```bash
docker-compose up -d
```

### 确保配置 IPFS API 以允许跨源（CORS）请求

运行以下命令：

```bash
docker exec ipfs_host ipfs config --json API.HTTPHeaders.Access-Control-Allow-Origin '["http://0.0.0.0:5001", "http://127.0.0.1:5001", "https://webui.ipfs.io"]'
docker exec ipfs_host ipfs config --json API.HTTPHeaders.Access-Control-Allow-Methods '["PUT", "GET", "POST"]'
```

### 移除默认的 boostrap 节点

```bash
docker exec ipfs_host ipfs bootstrap rm --all
```

### 重启服务

```bash
docker restart upfs_host
```

> 查看邻居

```bash
docker exec ipfs_host ipfs swarm peers
```

## 附录

> 查看运行日志

```bash
docker logs -f ipfs_host
```

> 停止容器

```bash
docker stop ipfs_host
```

> 删除容器

```bash
docker rm ipfs_host
```

> 重启容器

```bash
docker restart upfs_host
```

> 运行 IPFS 命令 

```bash
docker exec ipfs_host <ipfs cmd>
```
