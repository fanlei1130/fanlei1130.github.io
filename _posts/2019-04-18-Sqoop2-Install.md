---
layout:     post
title:      "HDP 3.1.0 集成 Sqoop2"
subtitle:   ""
date:       2019-04-18 20:04:00
author:     "Sitoi"
header-img: "img/post/images/sqoop-logo.jpg"
catalog:    true
tags:
    - HDP
    - Sqoop2
    - Ambari
    - 安装教程
---

# HDP 3.1.0 集成 Sqoop2

## 环境

- 由三台主机组成的 HDP 3.1.0 集群
- 配置好时间同步

## 步骤

- 下载 `Sqoop2` 的安装包
- 解压安装包到 `/usr/lib` 目录下
- 修改 `sqoop.sh` 环境变量
- 修改 `sqoop.properties` 配置
- 导入第三方 `jar` 包
- 配置第三方 `jar` 包引用路径
- 修改 `Ambari` 上组件配置
- 验证配置是否正确
- 开启服务器

### 下载 Sqoop2 的安装包

下载地址：[http://mirror.bit.edu.cn/apache/sqoop/1.99.7/](http://mirror.bit.edu.cn/apache/sqoop/1.99.7/)

下载命令

```bash
cd ~
wget http://mirror.bit.edu.cn/apache/sqoop/1.99.7/sqoop-1.99.7-bin-hadoop200.tar.gz
```

### 解压安装包到 `/usr/lib` 目录下

解压 `Sqoop2` 压缩包

```bash
tar -xvf sqoop-<version>-bin-hadoop<hadoop-version>.tar.gz
```

移动到 `/usr/lib/sqoop` 目录

```bash
mv sqoop-<version>-bin-hadoop<hadoop version> /usr/lib/sqoop
```

### 修改 sqoop.sh 环境变量

编辑 `/usr/lib/sqoop/bin/sqoop.sh` 文件

```bash
sudo vim /usr/lib/sqoop/bin/sqoop.sh
```

找到 `function sqoop_server_classpath_set` 函数，将其中的环境变量改一下就可以了，如下：

```text
function sqoop_server_classpath_set {
                                                                                             
  HADOOP_COMMON_HOME=${HADOOP_COMMON_HOME:-${HADOOP_HOME}/share/hadoop/common}
  HADOOP_HDFS_HOME=${HADOOP_HDFS_HOME:-${HADOOP_HOME}/share/hadoop/hdfs}
  HADOOP_MAPRED_HOME=${HADOOP_MAPRED_HOME:-${HADOOP_HOME}/share/hadoop/mapreduce}
  HADOOP_YARN_HOME=${HADOOP_YARN_HOME:-${HADOOP_HOME}/share/hadoop/yarn}
```

将这些环境变量都注释掉，改为下面的内容即可：

```text
function sqoop_server_classpath_set {

  HDP=/usr/hdp/3.0.1.0-187
  HADOOP_COMMON_HOME=$HDP/hadoop
  HADOOP_HDFS_HOME=$HDP/hadoop-hdfs
  HADOOP_MAPRED_HOME=$HDP/hadoop-mapreduce
  HADOOP_YARN_HOME=$HDP/hadoop-yarn
```

### 修改 sqoop.properties 配置

修改 `sqoop.properties`

```bash
sudo vim /usr/lib/sqoop/conf/sqoop.properties
```

找到 `org.apache.sqoop.submission.engine.mapreduce.configuration.directory` 参数，如下：

```text
org.apache.sqoop.submission.engine.mapreduce.configuration.directory=/etc/hadoop/conf/
```

根据集群实际信息将其改为下面的内容即可：

```text
org.apache.sqoop.submission.engine.mapreduce.configuration.directory=/usr/hdp/3.1.0.0-78/hadoop/conf/
```


### 导入第三方 jar 包

```bash
mkdir /usr/lib/sqoop/extra
cp /var/lib/ambari-server/resources/mysql-jdbc-driver.jar /usr/lib/sqoop/extra/
cp  -r /usr/lib/sqoop/extra/* /usr/lib/sqoop/server/lib/
cp  -r /usr/lib/sqoop/extra/* /usr/lib/sqoop/shell/lib/
cp  -r /usr/lib/sqoop/extra/* /usr/lib/sqoop/tools/lib/
```

### 配置第三方 jar 包引用路径

```bash
sudo vim ~/.bashrc
```

添加环境变量，如下：

```text
export SQOOP_HOME=/usr/lib/sqoop
export SQOOP_SERVER_EXTRA_LIB=$SQOOP_HOME/extra
export PATH=$PATH:$SQOOP_HOME/bin
```

运行如下命令，使环境变量生效：

```bash
source ~/.bashrc 
```

### 修改 Ambari 上组件配置


**修改组件 HDFS 配置**

|配置项|参数名|初始值|修改值|
|:---:|:---:|:---:|:---:|
|Advanced hdfs-site|dfs.permissions.enabled|True|False|
|Custom core-site|hadoop.proxyuser.hive.hosts| |`*`|
|Custom core-site|hadoop.proxyuser.root.hosts| |`*`|
|Custom core-site|hadoop.proxyuser.sqoop2.groups| |`*`|
|Custom core-site|hadoop.proxyuser.sqoop2.hosts| |`*`|
|Custom core-site|hadoop.proxyuser.yarn.groups| |`*`|
|Custom core-site|hadoop.proxyuser.yarn.hosts| |`*`|


**修改组件 MapRduce2 配置**

> 将 `${hdp.version}` 替换成实际 `hdp` 的版本: `3.1.0.0-78`

|配置项|参数名|初始值|修改值|
|:---:|:---:|:---:|:---:|
|Advanced mapred-site|mapreduce.admin.map.child.java.opts|`-server -XX:NewRatio=8 -Djava.net.preferIPv4Stack=true -Dhdp.version=${hdp.version}` |`-server -XX:NewRatio=8 -Djava.net.preferIPv4Stack=true -Dhdp.version=3.1.0.0-78` |
|Advanced mapred-site|mapreduce.admin.reduce.child.java.opts|`-server -XX:NewRatio=8 -Djava.net.preferIPv4Stack=true -Dhdp.version=${hdp.version}` |`-server -XX:NewRatio=8 -Djava.net.preferIPv4Stack=true -Dhdp.version=3.1.0.0-78` |
|Advanced mapred-site|mapreduce.admin.user.env|`LD_LIBRARY_PATH=/usr/hdp/${hdp.version}/hadoop/lib/native:/usr/hdp/${hdp.version}/hadoop/lib/native/Linux-{{architecture}}-64` | `LD_LIBRARY_PATH=/usr/hdp/3.1.0.0-78/hadoop/lib/native:/usr/hdp/3.1.0.0-78/hadoop/lib/native/Linux-{{architecture}}-64` |
|Advanced mapred-site|mapreduce.application.classpath|`$PWD/mr-framework/hadoop/share/hadoop/mapreduce/*:$PWD/mr-framework/hadoop/share/hadoop/mapreduce/lib/*:$PWD/mr-framework/hadoop/share/hadoop/common/*:$PWD/mr-framework/hadoop/share/hadoop/common/lib/*:$PWD/mr-framework/hadoop/share/hadoop/yarn/*:$PWD/mr-framework/hadoop/share/hadoop/yarn/lib/*:$PWD/mr-framework/hadoop/share/hadoop/hdfs/*:$PWD/mr-framework/hadoop/share/hadoop/hdfs/lib/*:$PWD/mr-framework/hadoop/share/hadoop/tools/lib/*:/usr/hdp/${hdp.version}/hadoop/lib/hadoop-lzo-0.6.0.${hdp.version}.jar:/etc/hadoop/conf/secure` |`$PWD/mr-framework/hadoop/share/hadoop/mapreduce/*:$PWD/mr-framework/hadoop/share/hadoop/mapreduce/lib/*:$PWD/mr-framework/hadoop/share/hadoop/common/*:$PWD/mr-framework/hadoop/share/hadoop/common/lib/*:$PWD/mr-framework/hadoop/share/hadoop/yarn/*:$PWD/mr-framework/hadoop/share/hadoop/yarn/lib/*:$PWD/mr-framework/hadoop/share/hadoop/hdfs/*:$PWD/mr-framework/hadoop/share/hadoop/hdfs/lib/*:$PWD/mr-framework/hadoop/share/hadoop/tools/lib/*:/usr/hdp/3.1.0.0-78/hadoop/lib/hadoop-lzo-0.6.0.3.1.0.0-78.jar:/etc/hadoop/conf/secure` |
|Advanced mapred-site|mapreduce.application.framework.path|`/hdp/apps/${hdp.version}/mapreduce/mapreduce.tar.gz#mr-framework` |`/hdp/apps/3.1.0.0-78/mapreduce/mapreduce.tar.gz#mr-framework` |
|Advanced mapred-site|yarn.app.mapreduce.am.admin-command-opts|`-Dhdp.version=${hdp.version}` |`-Dhdp.version=3.1.0.0-78` |
|Advanced mapred-site|MR AppMaster Java Heap Size|`-Xmx819m -Dhdp.version=${hdp.version}` |`-Xmx819m -Dhdp.version=3.1.0.0-78`|

### 验证配置是否正确

```bash
$ sqoop2-tool verify

Setting conf dir: /usr/lib/sqoop/bin/../conf                                                 
Sqoop home directory: /usr/lib/sqoop                                                         
Sqoop tool executor:                                                                         
        Version: 1.99.7                                                                      
        Revision: 435d5e61b922a32d7bce567fe5fb1a9c0d9b1bbb                                   
        Compiled on Tue Jul 19 16:08:27 PDT 2016 by abefine                                  
Running tool: class org.apache.sqoop.tools.tool.VerifyTool                                   
0    [main] INFO  org.apache.sqoop.core.SqoopServer  - Initializing Sqoop server.            
8    [main] INFO  org.apache.sqoop.core.PropertiesConfigurationProvider  - Starting config fi
le poller thread                                                                             
Verification was successful.                                                                 
Tool class org.apache.sqoop.tools.tool.VerifyTool has finished correctly.  
```

### 开启服务器

```bash
$ sqoop2-server start

Setting conf dir: /usr/lib/sqoop/bin/../conf                                                 
Sqoop home directory: /usr/lib/sqoop                                                         
Sqoop tool executor:                                                                         
        Version: 1.99.7                                                                      
        Revision: 435d5e61b922a32d7bce567fe5fb1a9c0d9b1bbb                                   
        Compiled on Tue Jul 19 16:08:27 PDT 2016 by abefine                                  
Running tool: class org.apache.sqoop.tools.tool.VerifyTool                                   
0    [main] INFO  org.apache.sqoop.core.SqoopServer  - Initializing Sqoop server.            
8    [main] INFO  org.apache.sqoop.core.PropertiesConfigurationProvider  - Starting config fi
le poller thread                                                                             
Verification was successful.                                                                 
Tool class org.apache.sqoop.tools.tool.VerifyTool has finished correctly.                    
[root@sandbox-hdp ~]# sqoop2-server start                                                    
Setting conf dir: /usr/lib/sqoop/bin/../conf                                                 
Sqoop home directory: /usr/lib/sqoop                                                         
Starting the Sqoop2 server...                                                                
0    [main] INFO  org.apache.sqoop.core.SqoopServer  - Initializing Sqoop server.            
11   [main] INFO  org.apache.sqoop.core.PropertiesConfigurationProvider  - Starting config fi
le poller thread                                                                             
Sqoop2 server started. 
```

> 查看是否启动成功

```bash
$ jps | grep Sqoop

30970 SqoopJettyServer
```

如出现 `SqoopJettyServer` 进程则表示已启动成功。