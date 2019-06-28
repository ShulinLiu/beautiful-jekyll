---
layout: post
title: Docker的基本使用
tags: [docker]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 1. 使用docker安装MySOL
使用docker pull命令：

```
docker pull mysql:5.6
```
其中“5.6“为版本号，也可以使用”latest“标签下载最新版本。

# 2. 在Docker下运行MySQL

```
docker run -p 3306:3306 --name mymysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=12345 -d mysql:latest
```

参数详解：
--name 后面的“mymysql”为自定义的容器名
-e 为设置容器变量。这里我们将mysql的密码设置为环境变量
docker run 是启动容器命令：i是交互式操作，t是一个终端，d 表示容器在后台运行

查看已运行的docker镜像，如下：
```
docker container ps
```

使用命令
```
docker exec -it mymysql bash
```
连接到mysql镜像中。
