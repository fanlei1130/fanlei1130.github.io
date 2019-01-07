---
layout:     post
title:      "Hadoop 安装教程"
subtitle:   ""
date:       2019-01-07 22:36:00
author:     "Shitao"
header-img: ""
catalog:    true
tags:
    - Hadoop
    - 安装教程
    - 大数据
    - Apache
    - 分布式
    - HDFS
    - MapReduce
---


# Hadoop 安装教程

## 安装环境：

- Fedora 29
- openjdk version "1.8.0_191"

## 安装步骤：

- 创建 `Hadoop` 帐号
- 下载 `Hadoop` 安装包
- 解压 `Hadoop` 安装包
- 配置环境变量
- 配置 `Hadoop` 文件
- 启动集群
- 查看状态

### 创建 `Hadoop` 帐号

##### 为 `Hadoop` 创建一个专门的账号

```bash
sudo adduser hadoop
sudo passwd hadoop
```

##### 授予 `Hadoop` `root` 权限

> 为了测试，图方便，这里给Hadoop root权限，生产环境不建议这样做。

使用root权限编辑/etc/sudoers:

```bash
sudo vim /etc/sudoers
```

末尾添加一行：

```text
hadoop  ALL=(ALL) ALL
```

切换到Hadoop账号：

```bash
su hadoop
```

##### 配置SSH无密码登录

**首先生成公私密钥对**

```bash
ssh-keygen -t rsa
```

> 指定 `key pair` 的存放位置
> 回车默认存放于/home/hadoop/.ssh/id_rsa输入passphrase，这里直接回车，为空，确保无密码可登陆。

**拷贝生成的公钥到授权key文件（authorized_keys)**

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

**改变key权限为拥有者可读可写（0600）**

```bash
chmod 0600 ~/.ssh/authorized_keys
```

chomod命令参考：
```
chmod 600 file – owner can read and write
chmod 700 file – owner can read, write and execute
chmod 666 file – all can read and write
chmod 777 file – all can read, write and execute
```

**测试是否成功**

```bash
ssh localhost
```

### 下载 `Hadoop` 安装包

```bash
cd ~
wget http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-3.1.1/hadoop-3.1.1.tar.gz
```

### 解压 `Hadoop` 安装包

> 最好做个关联 `ln -s hadoop-3.1.1 hadoop`

```
tar -zxvf hadoop-3.1.1.tar.gz
mv hadoop-3.1.1 hadoop
```

### 配置环境变量

编辑 `~/.bashrc` 文件

```bash
vim ~/.bashrc
```

添加以下环境变量

```text
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/home/hadoop/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native
```

使配置文件生效

```bash
source ~/.bashrc
```

### 配置 `Hadoop` 文件

修改 `hadoop/core-site.xml` 配置文件

```bash
vim $HADOOP_HOME/etc/hadoop/core-site.xml
```

修改以下内容：

```text
<configuration>
<property>
  <name>fs.default.name</name>
    <value>hdfs://localhost:9000</value>
</property>
</configuration>
```

修改 `hadoop/hdfs-site.xml` 配置文件

```bash
vim $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```

修改以下内容：

```text
<configuration>
<property>
 <name>dfs.replication</name>
 <value>1</value>
</property>

<property>
  <name>dfs.name.dir</name>
    <value>file:///home/hadoop/hadoopdata/hdfs/namenode</value>
</property>

<property>
  <name>dfs.data.dir</name>
    <value>file:///home/hadoop/hadoopdata/hdfs/datanode</value>
</property>
</configuration>
```

修改 `hadoop/mapred-site.xml` 配置文件

```bash
vim $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

修改以下内容：

```text
 <configuration>
 <property>
  <name>mapreduce.framework.name</name>
   <value>yarn</value>
 </property>
</configuration>
```

修改 `hadoop/yarn-site.xml` 配置文件

```bash
vim $HADOOP_HOME/etc/hadoop/yarn-site.xml
```

修改以下内容：

```text
<configuration>
 <property>
  <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
 </property>
</configuration>
```

### 启动集群

##### 格式化Hadoop文件系统

```bash
hdfs namenode -format
```


##### 启动HDFS

```
$HADOOP_HOME/sbin/start-dfs.sh
```

> 注：若是 `JAVA_HOME` 没设置错误

```
vim $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```

在末尾加上：

```text
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

##### 启动YARN：

```bash
$HADOOP_HOME/sbin/start-yarn.sh
```

### 查看状态

查看 `HDFS` 状态，浏览器访问： `http://localhost:9870`