---
layout: post
title: ElasticSearch 安装
subtitle: ElasticSear的安装及插件
date: 2018-07-25 13:32:20
author: Silence
header-img: ""
catalog: true
header-bg-css: "linear-gradient(to right, #404040, #404040);"
catalog: true
tags:
  - ElasticSearch
---

### 安装

- 首先从官网下载安装包
- 此处我们选择2.4.6这个版本,然后下载tar压缩包
- 下载以后直接解压，就算安装完成了
```
tar zxvf elasticsearch-2.4.6.tar.gz
```

### 配置

- 编辑elasticsearch配置文件
```shell
# 进入安装目录
cd elasticsearch-2.4.6
# 编辑配置文件
vi ./config/elasticsearch.yml
```

- 下面是几个常用的配置项
```shell
# 集群名称
cluster.name: elasticsearch
# 节点名称
node.name: node-1
# 数据存放路径
path.data: /path/to/data
# 日志存放路径
path.logs: /path/to/logs
# 配置访问IP 默认只能本机访问
network.host: 127.0.0.1
# 默认访问端口
http.port: 9200
```

### 创建用户

 - elasticsearch是不允许通过root用户运行的，所以先创建一个用户组和用户
```shell
# 创建用户组
groupadd es
# 创建用户
useradd -g es elastic
# 更改elasticsearch目录拥有者
chown elastic:es -R elasticsearch-2.4.6
# 切换到elastic用户
su elastic
```

### 启动ES
```shell
./bin/elaticsearch
```

- 在浏览器里面访问 http://127.0.0.1:9200，可看见返回下面信息

```json
{
    "name" : "Doop",
    "cluster_name" : "elasticsearch",
    "cluster_uuid" : "Bp2ixlGMRzCE8F6j9MMxxg",
    "version" : {
        "number" : "2.4.6",
        "build_hash" : "5376dca9f70f3abef96a77f4bb22720ace8240fd",
        "build_timestamp" : "2017-07-18T12:17:44Z",
        "build_snapshot" : false,
        "lucene_version" : "5.5.4"
    },
    "tagline" : "You Know, for Search"
}
```

### 安装elasticsearch-head插件

 - Elasticsearch Head是集群管理、数据可视化、增删改查、查询语句可视化工具。它的安装也很简单

```shell
# 在线安装elasticsearch-head插件
./bin/plugin install mobz/elasticsearch-head
```

等待安装成功后，重新启动elasicsearch，访问 http://127.0.0.1:9200/_plugin/head 即可看到图形管理界面了