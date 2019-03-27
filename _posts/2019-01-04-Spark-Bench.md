---
layout:     post
title:      "Spark-Bench 教程"
subtitle:   ""
date:       2019-01-04 20:09:00
author:     "Sitoi"
header-img: ""
catalog:    true
tags:
    - Spark
    - 基准测试
    - 教程
    - 计算引擎
    - 大数据
    - Apache
---


# Spark-Bench 教程

## 系统环境配置

操作系统：`centos7`

环境要求：安装 `JDK`, `Hadoop`, `Spark`，`mvn` 运行环境

## 步骤

1. 安装测试工具

    1. 安装 `wikixmlj`
    2. 安装 `SparkBench` 基准测试组件
    3. 单机环境安装方式

2. 根据实际环境配置测试环境

    1. 修改基本环境
    2. 配置 `Spark` 运行参数部分

3. 运行 `Spark-Bench` 测试

    1. 机器学习测试案例
    2. 图计算测试案例
    3. SQL 查询测试案例
    4. 流处理测试案例
    5. 其他测试案例

4. 查看测试结果

### 安装测试工具

> 所有步骤在 `hdfs` 账号下进行：

##### 安装 wikixmlj

克隆项目：[项目地址](https://github.com/synhershko/wikixmlj)

```bash
git clone https://github.com/synhershko/wikixmlj.git
```

进入项目目录进行 `mvn` 编译：

```bash
cd wikixmlj

mvn package -Dmaven.test.skip=true

mvn install -Dmaven.test.skip=true
```

##### 安装 SparkBench 基准测试组件

> 注： `ubuntu` 系统需要安装以下包:

```bash
sudo apt-get install libgfortran3
```

克隆项目：[项目地址](https://github.com/SparkTC/spark-bench/tree/legacy)

```bash
git clone -b legacy https://github.com/CODAIT/spark-bench.git
```

进入项目目录进行构建：

```bash
cd spark-bench/
./bin/build-all.sh
```


##### 单机环境安装方式

在其他机器上执行上述两步，将两个文件拷贝到需要测试的机器上的 `hdfs` 账号下

> 注：  `ubuntu` 系统需要安装额外包

### 根据实际环境配置测试环境

> 进入到 `spark-bench` 目录下修改 `conf` 目录下的 `env.sh`

```bash
cd spark-bench/
vim conf/env.sh
```

##### 修改基本环境

> 根据实际情况修改对应配置

```text
master="<master_ip_address>"                   根据实际情况修改master
MC_LIST=""
HADOOP_HOME=<HADOOP_HOME>                      根据实际情况修改HADOOP_HOME
SPARK_HOME=<SPARK_HOME>                        根据实际情况修改SPARK_HOME
HDFS_URL="hdfs://${master}:9000"               根据实际情况修改端口号
DATA_HDFS="hdfs://${master}:9000/SparkBench"   根据实际情况修改端口号
```

##### 配置 Spark 运行参数部分

> 修改`conf`目录下的`env.sh`

```text
SPARK_EXECUTOR_MEMORY=4G                       根据实际情况修改
export SPARK_DRIVER_MEMORY=4g                  根据实际情况修改
export SPARK_EXECUTOR_INSTANCES=1              根据实际情况修改
export SPARK_EXECUTOR_CORES=1                  根据实际情况修改
```

### 运行 Spark-Bench 测试

|测试案例|功能|
|:---:|:---:|
|机器学习测试案例|逻辑回归，支持向量机，矩阵分解|
|图计算测试案例| PageRank，SVD++，三角计数（Triangle Count）|
|SQL查询测试案例|Hive，RDDRelation|
|流处理测试案例| Twitter Tag ， Page View|
|其他测试案例|Kmeans，线性回归，决策树，最短路径，标签传播，连通图，强连通图|

##### 机器学习测试案例

> 进入到 `spark-bench` 目录下，在该目录下执行操作：

|测试案例|Workload|
|:---:|:---:|
|逻辑回归| LogisticRegression|
|支持向量机| SVM |
|矩阵分解| MatrixFactorization|

步骤：

1. 修改配置参数
2. 运行生成测试数据脚本
3. 运行相应案例测试脚本

###### 修改配置参数

>  `<Workload>` 根据测试案例表填写实际内容，下同:

```bash
vim <Workload>/conf/env.sh
```

###### 运行生成测试数据脚本

```bash
./<Workload>/bin/gen_data.sh
```

###### 运行相应案例测试脚本

```bash
./<Workload>/bin/run.sh
```

##### 图计算测试案例

> 进入到 `spark-bench` 目录下，在该目录下执行操作：

|测试案例|Workload|
|:---:|:---:|
|网页排名| PageRank|
|SVD++| SVDPlusPlus |
|三角计数| TriangleCount|

步骤：

1. 修改配置参数
2. 运行生成测试数据脚本
3. 运行相应案例测试脚本


##### 修改配置参数


```bash
vim <Workload>/conf/env.sh
```

##### 运行生成测试数据脚本

```bash
./<Workload>/bin/gen_data.sh
```

##### 运行相应案例测试脚本

```bash
./<Workload>/bin/run.sh
```

#### SQL 查询测试案例

> 进入到 `spark-bench` 目录下，在该目录下执行操作：

|测试案例|Workload|
|:---:|:---:|
|SQL查询|SQL|

步骤：

1. 修改配置参数
2. 运行生成测试数据脚本
3. 运行相应案例测试脚本



##### 修改配置参数

```bash
vim /<Workload>/conf/env.sh
```

##### 运行生成测试数据脚本

```bash
./<Workload>/bin/gen_data.sh
```

##### 运行相应案例测试脚本

```bash
./<Workload>/bin/run.sh
```


> 在运行 SQL 查询案例时，默认是运行其中的 RDDRelation 案例，如果要想运行其中的 Hive 案例可以执行下面代码：

```bash
./<Workload>/bin/run.sh hive
```

#### 流处理测试案例

> 进入到 `spark-bench` 目录下，在该目录下执行操作：

|测试案例|Workload|
|:---:|:---:|
|流处理|Streaming|

步骤：

1. 修改配置参数
2. 首先在一个终端中执行生成随机数据
3. 然后再另一个终端中执行

##### 修改配置参数

```bash
vim <Workload>/conf/env.sh
```

> 在运行流数据处理案例时，例如 TwitterTag，Streaming 逻辑回归


##### 首先在一个终端中执行生成随机数据

> 在执行脚本时必须要指定你要运行案例名字的参数，如下：

```bash
./<Workload>/bin/gen_data.sh TwitterPopularTags
```


##### 然后再另一个终端中执行

> 在执行脚本时必须要指定你要运行案例名字的参数，如下：

```bash
./<Workload>/bin/run.sh TwitterPopularTags
```

> 当然你也可以在 `<Workload>/conf/env.sh` 配置文件中指定你要运行的子案例的名称，通过修改 `subApp=TwitterPopularTags`

#### 其他测试案例

> 进入到 `spark-bench` 目录下，在该目录下执行操作：

|测试案例|Workload|
|:---:|:---:|
|Kmeans|Kmeans|
|线性回归|LinearRegression|
|决策树|DecisionTree|
|最短路径|ShortestPaths|
|标签传播|LabelPropagation|
|连通图|ConnectedComponent|
|强连通图|StronglyConnectedComponent|
|主成分分析|PCA|

步骤：

1. 修改配置参数
2. 运行生成测试数据脚本
3. 运行相应案例测试脚本

##### 修改配置参数

```bash
vim <Workload>/conf/env.sh
```

##### 运行生成测试数据脚本

```bash
./<Workload>/bin/gen_data.sh
```

##### 运行相应案例测试脚本

```bash
./<Workload>/bin/run.sh
```

### 查看测试结果

在 `spark-bench` 目录下的 `num` 目录下可以查看到运行结果。


### 相关链接

[开源项目官网](https://github.com/CODAIT/spark-bench/tree/legacy) : [https://github.com/CODAIT/spark-bench/tree/legacy](https://github.com/CODAIT/spark-bench/tree/legacy)

[安装文档](https://blog.csdn.net/xfg0218/article/details/79250019) : [https://blog.csdn.net/xfg0218/article/details/79250019](https://blog.csdn.net/xfg0218/article/details/79250019)
