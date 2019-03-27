---
layout:     post
title:      "Docker-Compose 创建容器教程"
subtitle:   ""
date:       2018-10-03 18:17:00
author:     "Sitoi"
header-img: ""
catalog:    true
tags:
    - Docker
    - Docker-Compose
    - 命令
    - 容器
    - 微服务
    - 教程
---


# Docker-compose（创建容器）

**样例**

```
version: "2"

services:
  spark-dashboard:
    build:
      context: .
    container_name: spark-dashboard
    image: docker.io/sitoi/spark-dashboard
    environment:
      - SPARK_TYPE=master
    ports:
      - 5000:5000
  redis:
    image: docker.io/redis:4
    ports:
      - '6379:6379'
    volumes:
      - ./db/redis:/data
    command: >
      --requirepass password
  neo4j:
    image: docker.io/neo4j:3.3
    ports:
     - "7474:7474"
     - "7687:7687"
    volumes:
    - ./db/neo4j:/data/

```


## docker-compose 命令

大多数 `Compose` 命令都是运行于一个或多个服务的，如果服务没有指定，该命令将会应用到所有服务，
如果要获得所有可用信息，使用命令：docker-compose [COMMAND] --help，下面是命令（COMMAND）的说明：

#### build

创建或者再建服务

服务被创建后会标记为 `project_service` (比如 `composetest_db`)，
如果改变了一个服务的 Dockerfile 或者构建目录的内容，可以使用 `docker-compose build` 来重建它

#### help

显示命令的帮助和使用信息

#### kill

通过发送 `SIGKILL` 的信号强制停止运行的容器，这个信号可以选择性的通过，比如：

```bash
docker-compose kill -s SIGKINT
```

#### logs

显示服务的日志输出

#### port

为端口绑定输出公共信息

#### ps

显示容器

#### pull

拉取服务镜像

#### rm

删除停止的容器

#### run

在服务上运行一个一次性命令，比如：

`docker-compose run web python manage.py shell`

#### scale

设置为一个服务启动的容器数量，数量是以这样的参数形式指定的：`service=num`，比如：

`docker-compose scale web=2 worker=3`

#### start

启动已经存在的容器作为一个服务

#### stop

停止运行的容器而不删除它们，它们可以使用命令`docker-compose start`重新启动起来

#### up

为一个服务构建、创建、启动、附加到容器

连接的服务会被启动，除非它们已经在运行了

默认情况下，`docker-compose up` 会集中每个容器的输出，当存在时，所有的容器会停止，运行 `docker-compose up -d` 会在后台启动容器并使它们运行

默认情况下，如果服务存在容器的话，`docker-compose up` 会停止并再创建它们（使用了 `volumes-from` 会保留已挂载的卷），
如果不想使容器停止并再创建的话，使用`docker-compose up --no-recreate`，如果有需要的话，这会启动任何停止的容器

### 选项

#### –verbose

显示更多输出

#### –version

显示版本号并退出

#### -f,–file FILE

指定一个可选的 `Compose yaml` 文件（默认：`docker-compose.yml`）

#### -p,–project-name NAME

指定可选的项目名称（默认：当前目录名称）

## docker-compose.yaml 文件说明

每一个定义在 `docker-compose.yml` 中的服务必须明确指定一个 `image` 或者 `build` 选项，
这与 `docker run` 命令行中输入的是对应相同的，对于`docker run`，
在 `Dockerfile` 文件中指定的选项（比如 `CMD`、`EXPOSE`、`VOLUME`、`ENV`）是默认的，因此不必在 `docker-compose.yml` 中再指定一次

#### image

标明 image 的 ID，这个 image ID 可以是本地也可以是远程的，如果本地不存在，Compose 会尝试去 `pull` 下来

```
image: ubuntu
image: orchardup/postgresql
image: a4bc65fd
```

#### build

该参数指定 `Dockerfile` 文件的路径，该目录也是发送到守护进程的构建环境（这句有点），Compose 将会以一个已存在的名称进行构建并标记，并随后使用这个 image

```
build: /path/to/build/dir
```

#### command

覆盖容器启动后默认执行的命令

```
command: bundle exec thin -p 3000
```

#### links

连接到其他服务中的容器，可以指定服务名称和这个链接的别名，或者只指定服务名称

```
links:
 - db
 - db:database
 - redis
```

此时，在容器内部，会在 /etc/hosts 文件中用别名创建一个条目，就像这样：

```
172.17.2.186  db
172.17.2.186  database
172.17.2.186  redis
```

环境变量也会被创建，关于环境变量的参数，会在后面讲到

#### external_links

连接到在这个 docker-compose.yml 文件或者 Compose 外部启动的容器，特别是对于提供共享和公共服务的容器。
在指定容器名称和别名时，`external_links` 遵循着和 `links` 相同的语义用法

```
external_links:
 - redis_1
 - project_db_1:mysql
 - project_db_1:postgresql
```

#### ports

暴露端口信息。

使用宿主：容器 （HOST:CONTAINER）格式或者仅仅指定容器的端口（宿主将会随机选择端口）都可以。

```
ports:
 - "3000"
 - "8000:8000"
 - "49100:22"
 - "127.0.0.1:8001:8001"
```

注：当使用 `HOST:CONTAINER` 格式来映射端口时，如果你使用的容器端口小于 60 你可能会得到错误得结果，
因为 YAML 将会解析 `xx:yy` 这种数字格式为 60 进制。所以建议采用字符串格式。

#### expose

暴露端口而不必向主机发布它们，而只是会向链接的服务 `linked service` 提供，只有内部端口可以被指定

```
expose:
 - "3000"
 - "8000"
```

#### volumes

挂载路径最为卷，可以选择性的指定一个主机上的路径（`主机：容器`），或是一种可使用的模式（`主机：容器：ro`）

```
volumes:
 - /var/lib/mysql
 - cache/:/tmp/cache
 - ~/configs:/etc/configs/:ro
```

#### volumes_from

从另一个服务或容器挂载它的所有卷。

```
volumes_from:
 - service_name
 - container_name
```

#### environment

加入环境变量，可以使用数组或者字典，只有一个 key 的环境变量可以在运行 Compose 的机器上找到对应的值，这有助于加密的或者特殊主机的值

```
environment:
  RACK_ENV: development
  SESSION_SECRET:
environments:
  - RACK_ENV=development
  - SESSION_SECRET
```

#### env_file

从文件中获取环境变量，可以为单独的文件路径或列表。

如果通过 `docker-compose -f FILE` 指定了模板文件，则 `env_file` 中路径会基于模板文件路径。

如果有变量名称与 `environment` 指令冲突，则以后者为准。

```
env_file:
  - .env

RACK_ENV: development
```

#### net

网络模式，可以在`docker客户端`的`--net`参数中指定这些值

```
net: "bridge"
net: "none"
net: "container:[name or id]"
net: "host"
```

#### dns

自定义DNS服务，可以是一个单独的值或者一张列表

```
dns: 8.8.8.8
dns:
  - 8.8.8.8
  - 9.9.9.9
```

#### cap_add,cap_drop

加入或者去掉容器能力，查看`man 7 capabilities` 可以有一张完整的列表

```
cap_add:
  - ALL

cap_drop:
  - NET_ADMIN
  - SYS_ADMIN
```

#### dns_search

自定义DNS搜索范围，可以是单独的值或者一张列表

```
dns_search: example.com
dns_search:
  - dc1.example.com
  - dc2.example.com
```

#### working_dir,entrypoint,user,hostname,domainname,mem_limit,privileged,restart,stdin_open,tty,cpu_shares

上述的每一个都只是一个单独的值，和docker run中对应的参数是一样的

```
cpu_shares: 73

working_dir: /code
entrypoint: /code/entrypoint.sh
user: postgresql

hostname: foo
domainname: foo.com

mem_limit: 1000000000
privileged: true

restart: always

stdin_open: true
tty: true
```


## Compose 环境变量说明

环境变量已经不再是用来连接服务的推荐方法了，相反，应该使用链接名称（默认情况下是链接服务的名称）作为主机名称来连接，这可以查看 docker-compose.yaml 的更多细节
Compose 使用 Docker links 来暴露服务的容器给其他的。每一个链接的容器都使用了一组环境变量，这每一组环境变量都是以容器名称的大写字母开头的
要查看服务可用的环境变量，运行 docker-compose run SERVICE env

#### name_PORT

完整URL，如：

```
DB_PORT=tcp//172.17.0.5:5432
```

#### name_PORT_num_protocol

完整URL，如：

```
DB_PORT_5432_TCP=tcp://172.17.0.5:5432
```

#### name_PORT_num_protocol_ADDR

容器的IP地址，如：

```
DB_PORT_5432_TCP_ADDR=172.17.0.5
```

#### name_PORT_num_protocol_PORT

暴露的端口号，如：

```
DB_PORT_5432_TCP_PORT=5432
```

#### name_PORT_num_protocol_PROTO

协议(tcp或者udp)，如：

```
DB_PORT_5432_TCP_PROTO=tcp
```

#### name_NAME

完全合格的容器名称，如：

```
DB_1_NAME=/myapp_web_1/myapp_db_1
```