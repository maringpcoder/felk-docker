# Filebeat+ELK stack (ELK) on Docker

[![elasticsearch](https://img.shields.io/badge/elasticsearch-6.4.0-blue.svg)](https://www.elastic.co/)
[![filebeat](https://img.shields.io/badge/filebeat-5.1-brightgreen.svg)](https://www.elastic.co/cn/products/beats/filebeat)
[![kibana](https://img.shields.io/badge/kibana-6.4.0-orange.svg)](https://www.elastic.co/downloads/kibana)

基于Elastic官方镜像
* [elasticsearch](https://github.com/elastic/elasticsearch-docker)
* [logstash](https://github.com/elastic/logstash-docker)
* [kibana](https://github.com/elastic/kibana-docker)

##目录

1.  [配置](#configuration)
    * [配置kibana](#怎样配置kibana?)
    * [配置logstash](#怎么配置logstash？)
    * [配置Elasticsearch](#怎么配置elasticsearch?)
2.  [存储](#存储)
    * [持久化elasticsarch数据](#如何持久化elasticksearch的数据)
3.  [jvm调优](#jvm-调优)
    * [设置服务使用的内容总量](#设置服务使用的内容总量)
4.  [运行服务](#运行服务)
    * [构建](#开始构建)    



##配置
**注意**:配置文件不是动态重载的，改了配置文件之后你需要重启一次docker服务.

###怎样配置Kibana?
默认配置文件在 `kibana/config/kibana.yml`.
也可以映射整个“config”目录,而不是单个文件.
###怎么配置Logstash？
默认配置文件在 `logstash/config/logstash.yml`.
###怎么配置Elasticsearch?
默认配置文件在 `elasticsearch/config/elasticsearch.yml`.

还可以通过环境变量直接指定要覆盖的选项

```yml
elasticsearch:

  environment:
    network.host: "_non_loopback_"
    cluster.name: "my-cluster"
```
##存储
###如何持久化Elasticksearch的数据
在Elasticsearch中存储的数据将在容器重启后持久化，而不是在容器移除后持久化。
如果你希望在容器移除后数据依然还在，需要挂在一个数据圈到容器中

```yml
elasticsearch:

  volumes:
    - /path/to/storage:/usr/share/elasticsearch/data
```

数据将会持久化到本地的 `/path/to/storage`目录

##jvm 调优
###设置服务使用的内容总量
Elasticsearch和Logstash的启动脚本可以从环境的值追加额外的JVM选项
变量，允许用户调整每个组件可以使用的内存量

| Service       | Environment variable |
|---------------|----------------------|
| Elasticsearch | ES_JAVA_OPTS         |
| Logstash      | LS_JAVA_OPTS         |

为了适应内存不足的环境(默认情况下，Mac的Docker只有2gb可用)，需要使用堆大小
默认情况下，“docker-compos”中的每个服务的分配上限为256MB。yml文件。如果要重写
默认JVM配置，在“docker- composition”中编辑匹配的环境变量。yml文件。
例如，要增加Logstash的最大JVM堆大小:

```yml
logstash:

  environment:
  
  
    LS_JAVA_OPTS: "-Xmx1g -Xms1g"
```
##运行服务
**注意**:如果您切换了分支或更新了基本映像，您可能需要先运行`docker-compose build`
###开始构建
```
$ docker-compose build
$ docker-compose up
```