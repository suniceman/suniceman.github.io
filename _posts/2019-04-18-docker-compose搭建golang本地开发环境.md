---
layout: post
title: docker-compose搭建golang本地开发环境
subtitle: Docker Golang
date: 2019-04-18 10:00:00
author: Silence
header-img: ""
catalog: true
header-bg-css: "linear-gradient(to right, #404040, #404040);"
tags:
  - docker
  - golang
---

### docker-compose搭建golang本地开发环境

> 目前仅使用到mysql，golang， redis

目录结构
	```
	yin5th@yin5th:~/code/docker/compose-golang$ tree
	.
	├── docker-compose.yml
	└── golang
	    └── Dockerfile
	```

1. docker-compose.yml

	```
	version: "3"
	services:
	  golang:
	    build: ./golang
	    ports:
	      - "8088:8088"
	    links:
	      - "mysql"
	      - "redis"
	    volumes:
	      - $HOME/workspace/go:/go
	    tty: true
	  mysql:
	    image: mysql:5.7
	    ports:
	      - "3306:33066"
	    volumes:
	      - /home/code/data/golang-mysql/:/var/lib/mysql/
	    environment:
	      MYSQL_ROOT_PASSWORD: 123456
	  redis:
	    image: redis
	    ports:
	      - "6379:63791"
	```

注意，上述代码中：
	1. golang容器下 tty: true 必须  否则在执行docker-compose up -d时 golang容器将退出

	1. golang容器下 volumes 是把本地所有的源码都映射到容器中。仅在本地开发时使用，上线部署时不可。

1. golang Dockerfile

	```
	FROM golang
	RUN apt-get update && apt-get install -y vim
	WORKDIR $GOPATH/src
	EXPOSE 8088
	```

1. 构建容器
	```
	docker-compose up -d
	```

1. 查看所有容器
	```
	☁  compose-golang  docker-compose ps
	      Name             Command             State              Ports       
	-------------------------------------------------------------------------
	composegolang_go   bash               Up                 0.0.0.0:8088->80 
	lang_1                                                   88/tcp           
	composegolang_my   docker-            Up                 3306/tcp,        
	sql_1              entrypoint.sh                         33060/tcp, 0.0.0 
	                   mysqld                                .0:3306->33066/t 
	                                                         cp               
	composegolang_re   docker-            Up                 6379/tcp, 0.0.0. 
	dis_1              entrypoint.sh                         0:6379->63791/tc 
	                   redis ...                             p         
	```

1. 进入golang容器 
	```
	☁  compose-golang   docker exec -it composegolang_golang_1 bash
	root@71ae8893978a:/go/src# 
	```

更新dockerfile或者docker-compose.yml文件后 在docker-compose.yml 同路径下执行 docker-compose down 然后再执行 docker-compose up -d
