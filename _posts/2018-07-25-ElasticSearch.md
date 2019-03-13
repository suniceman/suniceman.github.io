---
layout: post
title: 全文搜索引擎 Elasticsearch 入门
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

## 安装

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
上面代码中，请求9200端口，Elastic 返回一个 JSON 对象，包含当前节点、集群、版本等信息。

### 安装elasticsearch-head插件

 - Elasticsearch Head是集群管理、数据可视化、增删改查、查询语句可视化工具。它的安装也很简单

```shell
# 在线安装elasticsearch-head插件
./bin/plugin install mobz/elasticsearch-head
```

等待安装成功后，重新启动elasicsearch，访问 http://127.0.0.1:9200/_plugin/head 即可看到图形管理界面了

默认情况下，Elastic 只允许本机访问，如果需要远程访问，可以修改 Elastic 安装目录的`config/elasticsearch.yml`文件，去掉`network.host`的注释，将它的值改成`0.0.0.0`，然后重新启动 Elastic。

```
network.host: 0.0.0.0
```

上面代码中，设成`0.0.0.0`让任何人都可以访问。线上服务不要这样设置，要设成具体的 IP。

## 基本概念

### Node 与 Cluster

Elastic 本质上是一个分布式数据库，允许多台服务器协同工作，每台服务器可以运行多个 Elastic 实例。

单个 Elastic 实例称为一个节点（node）。一组节点构成一个集群（cluster）。

### Index

Elastic 会索引所有字段，经过处理后写入一个反向索引（Inverted Index）。查找数据的时候，直接查找该索引。

所以，Elastic 数据管理的顶层单位就叫做 Index（索引）。它是单个数据库的同义词。每个 Index （即数据库）的名字必须是小写。

下面的命令可以查看当前节点的所有 Index。

```
$ curl -X GET 'http://localhost:9200/_cat/indices?v'
```

### Document
Index 里面单条的记录称为 Document（文档）。许多条 Document 构成了一个 Index。

Document 使用 JSON 格式表示，下面是一个例子。

```
{
  "user": "张三",
  "title": "工程师",
  "desc": "数据库管理"
}
```
同一个 Index 里面的 Document，不要求有相同的结构（scheme），但是最好保持相同，这样有利于提高搜索效率。

### Type

Document 可以分组，比如 `weather` 这个 Index 里面，可以按城市分组（北京和上海），也可以按气候分组（晴天和雨天）。这种分组就叫做 Type，它是虚拟的逻辑分组，用来过滤 Document。

不同的 Type 应该有相似的结构（schema），举例来说，`id` 字段不能在这个组是字符串，在另一个组是数值。这是与关系型数据库的表的[一个区别](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html)。性质完全不同的数据（比如 `products`和`logs`）应该存成两个 Index，而不是一个 Index 里面的两个 Type（虽然可以做到）。

下面的命令可以列出每个 Index 所包含的 Type。

```
$ curl 'localhost:9200/_mapping?pretty=true'
```

## 新建和删除 Index
新建 Index，可以直接向 Elastic 服务器发出 PUT 请求。下面的例子是新建一个名叫 `weather` 的 Index。

```
$ curl -X PUT 'localhost:9200/weather'
```
服务器返回一个 JSON 对象，里面的acknowledged字段表示操作成功。

```
{
  "acknowledged":true,
  "shards_acknowledged":true
}
```
然后，我们发出 DELETE 请求，删除这个 Index。

```
$ curl -X DELETE 'localhost:9200/weather'
```

## 中文分词设置
首先，安装中文分词插件。这里使用的是 [ik](https://github.com/medcl/elasticsearch-analysis-ik/)，也可以考虑其他插件（比如 [smartcn](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-smartcn.html)）。

```
$ ./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v5.5.1/elasticsearch-analysis-ik-5.5.1.zip
```
上面代码安装的是5.5.1版的插件，与 Elastic 5.5.1 配合使用。

接着，重新启动 Elastic，就会自动加载这个新安装的插件。

然后，新建一个 Index，指定需要分词的字段。这一步根据数据结构而异，下面的命令只针对本文。基本上，凡是需要搜索的中文字段，都要单独设置一下。

```
$ curl -X PUT 'localhost:9200/accounts' -d '
{
  "mappings": {
    "person": {
      "properties": {
        "user": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        },
        "title": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        },
        "desc": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        }
      }
    }
  }
}'
```

上面代码中，首先新建一个名称为 `accounts` 的 `Index` ，里面有一个名称为 `person` 的 Type。`person` 有三个字段。
```
user
title
desc
```

这三个字段都是中文，而且类型都是文本（text），所以需要指定中文分词器，不能使用默认的英文分词器。

Elastic 的分词器称为 [analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis.html)。我们对每个字段指定分词器。

```
"user": {
  "type": "text",
  "analyzer": "ik_max_word",
  "search_analyzer": "ik_max_word"
}
```
上面代码中，`analyzer` 是字段文本的分词器，`search_analyzer` 是搜索词的分词器。`ik_max_word` 分词器是插件 `ik` 提供的，可以对文本进行最大数量的分词。

## 数据操作

### 新增记录

向指定的 /Index/Type 发送 PUT 请求，就可以在 Index 里面新增一条记录。比如，向 `/accounts/person` 发送请求，就可以新增一条人员记录。

```
$ curl -X PUT 'localhost:9200/accounts/person/1' -d '
{
  "user": "张三",
  "title": "工程师",
  "desc": "数据库管理"
}'
```
服务器返回的 JSON 对象，会给出 Index、Type、Id、Version 等信息。

```
{
  "_index":"accounts",
  "_type":"person",
  "_id":"1",
  "_version":1,
  "result":"created",
  "_shards":{"total":2,"successful":1,"failed":0},
  "created":true
}
```

如果你仔细看，会发现请求路径是 `/accounts/person/1` ，最后的 `1` 是该条记录的 `Id`。它不一定是数字，任意字符串（比如`abc`）都可以。

新增记录的时候，也可以不指定 Id，这时要改成 POST 请求。
```
$ curl -X POST 'localhost:9200/accounts/person' -d '
{
  "user": "李四",
  "title": "工程师",
  "desc": "系统管理"
}'
```

上面代码中，向 `/accounts/person` 发出一个 POST 请求，添加一个记录。这时，服务器返回的 JSON 对象里面，`_id` 字段就是一个随机字符串。

```
{
  "_index":"accounts",
  "_type":"person",
  "_id":"AV3qGfrC6jMbsbXb6k1p",
  "_version":1,
  "result":"created",
  "_shards":{"total":2,"successful":1,"failed":0},
  "created":true
}
```

注意，如果没有先创建 Index（这个例子是`accounts`），直接执行上面的命令，Elastic 也不会报错，而是直接生成指定的 Index。所以，打字的时候要小心，不要写错 Index 的名称。

### 查看记录
向 `/Index/Type/Id` 发出 GET 请求，就可以查看这条记录。
```
$ curl 'localhost:9200/accounts/person/1?pretty=true'
```

上面代码请求查看 `/accounts/person/1` 这条记录，URL 的参数 `pretty=true` 表示以易读的格式返回。

返回的数据中，`found` 字段表示查询成功，`_source` 字段返回原始记录。
```
{
  "_index" : "accounts",
  "_type" : "person",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : {
    "user" : "张三",
    "title" : "工程师",
    "desc" : "数据库管理"
  }
}
```
如果 Id 不正确，就查不到数据，`found` 字段就是 `false`。
```
$ curl 'localhost:9200/weather/beijing/abc?pretty=true'

{
  "_index" : "accounts",
  "_type" : "person",
  "_id" : "abc",
  "found" : false
}
```

### 删除记录
删除记录就是发出 DELETE 请求。
```
$ curl -X DELETE 'localhost:9200/accounts/person/1'
```
这里先不要删除这条记录，后面还要用到。

### 更新记录
更新记录就是使用 PUT 请求，重新发送一次数据。
```
$ curl -X PUT 'localhost:9200/accounts/person/1' -d '
{
    "user" : "张三",
    "title" : "工程师",
    "desc" : "数据库管理，软件开发"
}'

{
  "_index":"accounts",
  "_type":"person",
  "_id":"1",
  "_version":2,
  "result":"updated",
  "_shards":{"total":2,"successful":1,"failed":0},
  "created":false
}
```

上面代码中，我们将原始数据从"数据库管理"改成"数据库管理，软件开发"。 返回结果里面，有几个字段发生了变化。
```
"_version" : 2,
"result" : "updated",
"created" : false
```

可以看到，记录的 Id 没变，但是版本（version）从 `1` 变成 `2` ，操作类型（result）从 `created` 变成 `updated` ，`created` 字段变成 `false` ，因为这次不是新建记录。

## 数据查询

### 返回所有记录
使用 GET 方法，直接请求 `/Index/Type/_search` ，就会返回所有记录。
```
$ curl 'localhost:9200/accounts/person/_search'

{
  "took":2,
  "timed_out":false,
  "_shards":{"total":5,"successful":5,"failed":0},
  "hits":{
    "total":2,
    "max_score":1.0,
    "hits":[
      {
        "_index":"accounts",
        "_type":"person",
        "_id":"AV3qGfrC6jMbsbXb6k1p",
        "_score":1.0,
        "_source": {
          "user": "李四",
          "title": "工程师",
          "desc": "系统管理"
        }
      },
      {
        "_index":"accounts",
        "_type":"person",
        "_id":"1",
        "_score":1.0,
        "_source": {
          "user" : "张三",
          "title" : "工程师",
          "desc" : "数据库管理，软件开发"
        }
      }
    ]
  }
}
```
上面代码中，返回结果的 `took` 字段表示该操作的耗时（单位为毫秒），`timed_out` 字段表示是否超时，`hits` 字段表示命中的记录，里面子字段的含义如下。

```
- total：返回记录数，本例是2条。
- max_score：最高的匹配程度，本例是1.0。
- hits：返回的记录组成的数组。
```

返回的记录中，每条记录都有一个`_score` 字段，表示匹配的程序，默认是按照这个字段降序排列。

### 全文搜索

Elastic 的查询非常特别，使用自己的[查询语法](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl.html)，要求 GET 请求带有数据体。
```
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "软件" }}
}'
```

上面代码使用 [Match](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl-match-query.html) 查询，指定的匹配条件是desc字段里面包含"软件"这个词。返回结果如下。
```

{
  "took":3,
  "timed_out":false,
  "_shards":{"total":5,"successful":5,"failed":0},
  "hits":{
    "total":1,
    "max_score":0.28582606,
    "hits":[
      {
        "_index":"accounts",
        "_type":"person",
        "_id":"1",
        "_score":0.28582606,
        "_source": {
          "user" : "张三",
          "title" : "工程师",
          "desc" : "数据库管理，软件开发"
        }
      }
    ]
  }
}
```
Elastic 默认一次返回10条结果，可以通过`size`字段改变这个设置。

```
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "管理" }},
  "size": 1
}'
```
上面代码指定，每次只返回一条结果。

还可以通过`from`字段，指定位移。
```

$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "管理" }},
  "from": 1,
  "size": 1
}'
```
上面代码指定，从位置1开始（默认是从位置0开始），只返回一条结果。

### 逻辑运算

如果有多个搜索关键字， Elastic 认为它们是`or`关系。

```
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "软件 系统" }}
}'
```

上面代码搜索的是`软件 or 系统`。
如果要执行多个关键词的`and`搜索，必须使用布尔查询。
```
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "desc": "软件" } },
        { "match": { "desc": "系统" } }
      ]
    }
  }
}'

```

## 、参考链接
 - [ElasticSearch 官方手册](https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started.html)
 - [A Practical Introduction to Elasticsearch](https://www.elastic.co/blog/a-practical-introduction-to-elasticsearch)