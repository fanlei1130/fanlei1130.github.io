---
layout:     post
title:      "Ubuntu 开发环境搭建教程"
subtitle:   ""
date:       2019-03-28 18:46:00
author:     "Sitoi"
header-img: "img/post/bg/ubuntu.jpg"
catalog:    true
tags:
    - 开发环境
    - Ubuntu
    - Linux
    - 教程
---


# Ubuntu 开发环境搭建教程

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

#### 免 sudo 密码

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

#### jdk java 开发环境

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

#### ibus-pinyin 中文输入法

```bash
sudo apt install ibus-pinyin
```

- 重启系统

- 进入语言设置

- 选择中文输入法 `chines` 然后找 `pinyin`

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

#### 安装 npm

```bash
nvm install node
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

> 进入 `maven` 文件夹，在 `conf` 目录中找到 `settings.xml` 文件

```bash
sudo vim /usr/share/maven/conf/settings.xml
```

> 配置 `mirrors` 的子节点，添加如下 `mirror`

```xml
<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>central</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```


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

#### 更换 gem 源

```bash
sudo gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
```
