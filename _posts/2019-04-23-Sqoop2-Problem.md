---
layout:     post
title:      "HDP 3.1.0 集成 Sqoop2 踩坑问题记录"
subtitle:   ""
date:       2019-04-23 19:20:00
author:     "Sitoi"
header-img: "img/post/images/sqoop-logo.jpg"
catalog:    true
tags:
    - HDP
    - Sqoop2
    - Ambari
    - 问题
---

# HDP 3.1.0 集成 Sqoop2 踩坑问题记录

## 问题一

```bash
$ sqoop:000> start job -n mysql2hdfs

2019-04-18 03:29:10 UTC: FAILURE_ON_SUBMIT 
Exception: java.lang.IllegalArgumentException: Unable to parse '/hdp/apps/${hdp.version}/mapreduce/mapreduce.tar.gz#mr-framework' as a URI, check the setting for mapreduce.application.framework.path
Stack trace: java.lang.IllegalArgumentException: Unable to parse '/hdp/apps/${hdp.version}/mapreduce/mapreduce.tar.gz#mr-framework' as a URI, check the setting for mapreduce.application.framework.path
	...
Caused by: java.net.URISyntaxException: Illegal character in path at index 11: /hdp/apps/${hdp.version}/mapreduce/mapreduce.tar.gz#mr-framework
	...
```

**原因：**

`sqoop2` 未设置该环境 ${hdp.version}

**解决：**

修改 Ambari 组件 MapRduce2 配置

> 将 `${hdp.version}` 替换成实际 `hdp` 的版本: `3.1.0.0-78`

|配置项|参数名|初始值|修改值|
|:---:|:---:|:---:|:---:|
|Advanced mapred-site|mapreduce.application.framework.path|`/hdp/apps/${hdp.version}/mapreduce/mapreduce.tar.gz#mr-framework` |`/hdp/apps/3.1.0.0-78/mapreduce/mapreduce.tar.gz#mr-framework` |

## 问题二

```bash
$ sqoop:000> status job -n mysql2hdfs 

Submission details
Job Name: demo8020
Server URL: http://localhost:12000/sqoop/
Created by: root
Creation date: 2019-04-18 03:29:55 UTC
Lastly updated by: root
External ID: job_1555557995737_0002
	http://xxx.xxx.xxx:8088/proxy/application_1555557995737_0002/
2019-04-18 03:30:10 UTC: FAILED 
Exception: Job Failed with status:3
Stack trace: Application application_1555557995737_0002 failed 2 times due to Error launching appattempt_1555557995737_0002_000002. Got exception: org.apache.hadoop.yarn.exceptions.YarnException: Unauthorized request to start container.
This token is expired. current time is 1555581323379 found 1555558796928
Note: System times on machines may be out of sync. Check system time and time zones.
	...
```

**原因：**

Ambari 时区没有同步 

**解决：**

利用 ntpd 同步时区

> 参考教程：[ntpd 详细教程](/2019/04/22/Ambari-ntpd/)

## 问题三


```bash
$ sqoop:000> status job -n demo8020 

Submission details
Job Name: demo8020
Server URL: http://localhost:12000/sqoop/
Created by: root
Creation date: 2019-04-18 06:01:15 UTC
Lastly updated by: root
External ID: job_1555566883587_0003
	http://xxx.xxx.xxx:8088/proxy/application_1555566883587_0003/
2019-04-18 06:01:34 UTC: FAILED 
Exception: Job Failed with status:3
Stack trace: Application application_1555566883587_0003 failed 2 times due to AM Container for appattempt_1555566883587_0003_000002 exited with  exitCode: 1
Failing this attempt.Diagnostics: [2019-04-18 14:01:16.003]Exception from container-launch.
Container id: container_e09_1555566883587_0003_02_000001
Exit code: 1

[2019-04-18 14:01:16.004]Container exited with a non-zero exit code 1. Error file: prelaunch.err.
Last 4096 bytes of prelaunch.err :
/hadoop/yarn/local/usercache/root/appcache/application_1555566883587_0003/container_e09_1555566883587_0003_02_000001/launch_container.sh: line 38: $PWD:$PWD/mr-framework/hadoop/share/hadoop/mapreduce/*:$PWD/mr-framework/hadoop/share/hadoop/mapreduce/lib/*:$PWD/mr-framework/hadoop/share/hadoop/common/*:$PWD/mr
```

**原因：**

`sqoop2` 未设置该环境 ${hdp.version}

**解决：**

修改 Ambari 组件 MapRduce2 配置

> 将 `${hdp.version}` 替换成实际 `hdp` 的版本: `3.1.0.0-78`

|配置项|参数名|初始值|修改值|
|:---:|:---:|:---:|:---:|
|Advanced mapred-site|mapreduce.admin.map.child.java.opts|`-server -XX:NewRatio=8 -Djava.net.preferIPv4Stack=true -Dhdp.version=${hdp.version}` |`-server -XX:NewRatio=8 -Djava.net.preferIPv4Stack=true -Dhdp.version=3.1.0.0-78` |
|Advanced mapred-site|mapreduce.admin.reduce.child.java.opts|`-server -XX:NewRatio=8 -Djava.net.preferIPv4Stack=true -Dhdp.version=${hdp.version}` |`-server -XX:NewRatio=8 -Djava.net.preferIPv4Stack=true -Dhdp.version=3.1.0.0-78` |
|Advanced mapred-site|mapreduce.admin.user.env|`LD_LIBRARY_PATH=/usr/hdp/${hdp.version}/hadoop/lib/native:/usr/hdp/${hdp.version}/hadoop/lib/native/Linux-{{architecture}}-64` | `LD_LIBRARY_PATH=/usr/hdp/3.1.0.0-78/hadoop/lib/native:/usr/hdp/3.1.0.0-78/hadoop/lib/native/Linux-{{architecture}}-64` |
|Advanced mapred-site|mapreduce.application.classpath|`$PWD/mr-framework/hadoop/share/hadoop/mapreduce/*:$PWD/mr-framework/hadoop/share/hadoop/mapreduce/lib/*:$PWD/mr-framework/hadoop/share/hadoop/common/*:$PWD/mr-framework/hadoop/share/hadoop/common/lib/*:$PWD/mr-framework/hadoop/share/hadoop/yarn/*:$PWD/mr-framework/hadoop/share/hadoop/yarn/lib/*:$PWD/mr-framework/hadoop/share/hadoop/hdfs/*:$PWD/mr-framework/hadoop/share/hadoop/hdfs/lib/*:$PWD/mr-framework/hadoop/share/hadoop/tools/lib/*:/usr/hdp/${hdp.version}/hadoop/lib/hadoop-lzo-0.6.0.${hdp.version}.jar:/etc/hadoop/conf/secure` |`$PWD/mr-framework/hadoop/share/hadoop/mapreduce/*:$PWD/mr-framework/hadoop/share/hadoop/mapreduce/lib/*:$PWD/mr-framework/hadoop/share/hadoop/common/*:$PWD/mr-framework/hadoop/share/hadoop/common/lib/*:$PWD/mr-framework/hadoop/share/hadoop/yarn/*:$PWD/mr-framework/hadoop/share/hadoop/yarn/lib/*:$PWD/mr-framework/hadoop/share/hadoop/hdfs/*:$PWD/mr-framework/hadoop/share/hadoop/hdfs/lib/*:$PWD/mr-framework/hadoop/share/hadoop/tools/lib/*:/usr/hdp/3.1.0.0-78/hadoop/lib/hadoop-lzo-0.6.0.3.1.0.0-78.jar:/etc/hadoop/conf/secure` |
|Advanced mapred-site|yarn.app.mapreduce.am.admin-command-opts|`-Dhdp.version=${hdp.version}` |`-Dhdp.version=3.1.0.0-78` |
|Advanced mapred-site|MR AppMaster Java Heap Size|`-Xmx819m -Dhdp.version=${hdp.version}` |`-Xmx819m -Dhdp.version=3.1.0.0-78`|


## 问题四

```bash
$ sqoop:000> start job -n demo

Exception has occurred during processing command 
Exception: org.apache.sqoop.common.SqoopException Message: GENERIC_HDFS_CONNECTOR_0007:Invalid input/output directory - Unexpected exception
Stack trace:
	 ...
Caused by: Exception: java.io.IOException Message: Failed on local exception: org.apache.hadoop.ipc.RpcException: RPC response exceeds maximum data length; Host Details : local host is: "xxx.xxx.xxx/192.168.1.151"; destination host is: "xxx.xxx.xxx":9000; 
Stack trace:
	 ... 
Caused by: Exception: java.lang.Throwable Message: RPC response exceeds maximum data length
Stack trace:
	 ... 
Caused by: Exception: java.lang.Throwable Message: RPC response exceeds maximum data length
```

**原因：**

hdfs link 信息填写错误 错误示例：hdfs://ip:9000 

**解决：**

将端口从 9000 改为 8020 正确示例：hdfs://ip:8020 