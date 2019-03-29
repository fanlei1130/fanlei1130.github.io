---
layout:     post
title:      "Dockerfile 创建镜像教程"
subtitle:   ""
date:       2018-10-02 18:17:00
author:     "Sitoi"
header-img: "img/post/bg/docker.png"
catalog:    true
tags:
    - Docker
    - Dockerfile
    - 命令
    - 容器
    - 微服务
    - 教程
---


# Dockerfile（从无到有创建镜像）

## 结构

DockerFile分为四部分组成：

- 基础镜像信息
- 维护者信息
- 镜像操作指令
- 容器启动时执行指令

#### 基础镜像信息

第一行必须指定基于的基础镜像

```Dockerfile
FROM alpine:3.8-python-3.6
```

#### 维护者信息

```Dockerfile
MAINTAINER Shi Tao <shitao0418@gamil.com>
```

#### 镜像操作指令

```Dockerfile
WORKDIR /opt/app
ENV HOME /opt/app
ADD . /opt/app

RUN pip3.6 install -r requirements.txt --upgrade -i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple

EXPOSE 8000
```


#### 容器启动时执行指令

```Dockerfile
ENTRYPOINT ["gunicorn"]
CMD ["-b 0.0.0.0:8000", "application:app"]
```



## 指定

#### From 指令

格式为 `From` 或者 `From:`

DockerFile 第一条必须为 `From` 指令。如果同一个 DockerFile 创建多个镜像时，可使用多个 `From` 指令（每个镜像一次）

**样例**

```Dockerfile
FROM alpine:3.8-python-3.6
```

#### MAINTAINER

格式为 `MAINTAINER`，指定维护者的信息

**样例**

```Dockerfile
MAINTAINER Shi Tao <shitao0418@gmail.com>
```


#### WORKDIR

格式为 `WORKDIR /path/to/workdir` 。

为后续的 `RUN`、`CMD`、`ENTRYPOINT` 指令配置工作目录。

可以使用多个 `WORKDIR` 指令，后续命令如果参数是相对路径，则会基于之前命令指定的路径。

**样例**

```
WORKDIR /a
WORKDIR b
WORKDIR c
```

`RUN pwd`

则最终路径为 `/a/b/c`。

#### ENV

格式为 `ENV` 。 指定一个环境变量，会被后续 `RUN` 指令使用，并在容器运行时保持。

**样例**

```
ENV HOME /app
ENV LOGNAME=root
ENV USER=root
```

#### RUN

格式为 `RUN` 在 shell 终端上运行，即 /bin/sh -C

每条 `RUN` 指令在当前基础镜像执行，并且提交新镜像。当命令比较长时，可以使用 / 换行。

#### ADD

格式为 `ADD`。

该命令将复制指定的 到容器中的 。 其中可以是 Dockerfile 所在目录的一个相对路径；也可以是一个 URL；还可以是一个 tar文件（自动解压为目录）

**样例**

```
ADD . /app/
```

#### COPY

格式为 `COPY` 。

复制本地主机的 （为 Dockerfile 所在目录的相对路径）到容器中的 。

当使用本地目录为源目录时，推荐使用 `COPY`。

#### VOLUME

格式为 `VOLUME ["/data"]` 。

创建一个可以从本地主机或其他容器挂载的挂载点，一般用来存放数据库和需要保持的数据等。

#### USER

格式为 `USER daemon`。

指定运行容器时的用户名或 UID，后续的 `RUN` 也会使用指定用户。

当服务不需要管理员权限时，可以通过该命令指定运行用户。并且可以在之前创建所需要的用户。

**样例**

```Dockerfile
RUN groupadd -r postgres && useradd -r -g postgres postgres
```

要临时获取管理员权限可以使用 gosu，而不推荐 sudo。

#### ONBUILD

格式为 `ONBUILD [INSTRUCTION]`

配置当所创建的镜像作为其它新创建镜像的基础镜像时，所执行的操作指令。

例如，Dockerfile 使用如下的内容创建了镜像 image-A。

```
[…]
ONBUILD ADD . /app/src
ONBUILD RUN /usr/local/bin/python-build –dir /app/src
[…]
```

如果基于 A 创建新的镜像时，新的 Dockerfile 中使用 `FROM image-A` 指定基础镜像时，会自动执行 `ONBUILD` 指令内容，等价于在后面添加了两条指令。

```
FROM image-A

#Automatically run the following
ADD . /app/src
RUN /usr/local/bin/python-build --dir /app/src
```

使用 `ONBUILD` 指令的镜像，推荐在标签中注明，例如 ruby:1.9-onbuild。

#### EXPOSE

格式为 `EXPOSE […]`

告诉 Docker 服务端容器暴露的端口号，供互联系统使用。在启动 Docker 时，可以通过 -P,主机会自动分配一个端口号转发到指定的端口。使用 -P，则可以具体指定哪个本地端口映射过来

**样例**

`EXPOSE 80`

#### ENTRYPOINT

两种格式：

```Dockerfile
ENTRYPOINT ["executable", "param1", "param2"]
```

```Dockerfile
ENTRYPOINT command param1 param2
```

配置容器启动后执行的命令，并且不可被 `docker run` 提供的参数覆盖。

每个 Dockerfile 中只能有一个 `ENTRYPOINT`，当指定多个时，只有最后一个起效。

#### CMD 指令

支持三种格式：

使用 exec 执行，**推荐**

```dockerfile
CMD ["executable" ,"Param1", "param2"]
```

在 /bin/sh 上执行

```dockerfile
CMD command param1 param2
```

提供给 `ENTRYPOINT` 做默认参数

```dockerfile
CMD ["Param1", "param2"]
```

每个容器只能执行一条 `CMD` 命令，多个 `CMD` 命令时，只最后一条被执行。