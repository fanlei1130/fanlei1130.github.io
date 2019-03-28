---
layout:     post
title:      "Ubuntu 开发环境安装"
subtitle:   ""
date:       2019-03-28 18:46:00
author:     "Sitoi"
header-img: ""
catalog:    true
tags:
    - 开发环境
    - Ubuntu
    - Linux
    - 安装
    - 教程
---


# Ubuntu 环境搭建教程

#### 更新

```bash
sudo apt upgrade
sudo apt update
```

#### 生成本机密钥

```bash
ssh-keygen -t rsa -C "shitao0418@gamil.com"
```

默认位置： `~/.ssh/`


#### 安装 vim

```bash
sudo apt install vim
```

## 配置

### 免 sudo 密码

```bash
echo -e 'Defaults:shitao !requiretty\nshitao ALL = (root) NOPASSWD:ALL' | sudo tee /etc/sudoers.d/shitao
```

## 安装软件

#### htop 可视化系统监测

```bash
sudo apt install htop
```

#### ssh 远程链接软件

```bash
sudo apt-get install openssh-server openssh-client
```

#### jdk java开发环境

```bash
sudo apt install openjdk-8-jdk 
```

#### python pip 开发工具

```bash
sudo apt install python3 python3-pip
```

> pip 升级

```bash
pip3 install --upgrade pip --user
```

#### curl 

```bash
sudo apt install curl
```

#### 安装网络相关包

```bash
sudo apt install net-tools
```

#### docker 安装

```bash
sudo apt install docker.io
```

#### 中文输入法

```bash
sudo apt install ibus-pinyin
```

#### chromium 浏览器

```bash
sudo apt install chromium-browser
```

> 全部安装上述全部软件

```bash
sudo apt install htop openssh-server openssh-client openjdk-8-jdk python3 python3-pip curl net-tools docker.io ibus-pinyin chromium-browser
```

#### 安装 nvm

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```


## 更换源

#### 更换 pipy 源

```bash
mkdir ~/.pip
vim ~/.pip/pip.conf
```

文件样例：

```text
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```


#### 更换 maven 源

TODO


#### 更换 npm 源

```bash
npm config set registry https://registry.npm.taobao.org
```

#### 更换 docker 源

```bash
sudo vim /etc/docker/daemon.json
```

```json
{
    "insecure-registries":[

    ],
    "registry-mirrors":[
        "https://registry.docker-cn.com"
    ]
}
```

