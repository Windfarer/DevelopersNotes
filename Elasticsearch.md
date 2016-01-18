# Elasticsearch

Elasticsearch是一个分布式搜索引擎，易于扩展，配置简单，自带Restful接口，易于使用。但这玩意是内存大户，比较吃硬件，官方推荐生产环境8G以上内存。
一些比较好看的官方参考资料：

  [权威指南](https://www.elastic.co/guide/en/elasticsearch/guide/current/index.html)
  
  [参考手册](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)

有一些权威指南的中文翻译，但普遍翻得稀烂，就不推荐了。

权威指南本身写得还是很好的，简单易懂，而且写得很有趣，推荐从这个入手。参考手册看起来就比较痛苦了，建议当字典查。

本文基于1.7版本进行撰写，更高版本未进行过测试，有些地方可能有较大变化

## 安装

到官网下载安装

安装后程序的各种相关路径（在之后配置分词插件时会用到）：

系统服务设置

## 前端插件安装

_head_用来管理集群上的数据，可以进行查询

```
bin/plugin -install mobz/elasticsearch-head
```

_marvel_官方出的监控面板，收费的，不过可以development trial

```
bin/plugin -i elasticsearch/marvel/latest
```

## 客户端

因为es提供restful接口，所以直接自己生成json、自己发动请求也是可以的。不过仍有一大堆客户端可以让你更方便的进行使用。

客户端列表 https://www.elastic.co/guide/en/elasticsearch/client/index.html

这里我用[elasticsearch-dsl-py](https://github.com/elastic/elasticsearch-dsl-py)，官方出品，比较好用，文档略坑

## 分词插件

目前用IK，效果还不错，项目主页

### 安装步骤

1. 去elasticsearch-rtf项目，切换到你相应的版本的branch（这里亲测，1.7版本的es，用1.6.0分支下的文件也是可以的），然后下载整个项目。
2. 将plugins目录下的analysis-ik目录，整个复制到自己的es的plugins目录下
3. 将config目录下的ik目录，整个复制到自己的es的config目录下
4. 修改config目录下的elasticsearch.yml配置文件，加一段

```
index:

  analysis:                   

    analyzer:      

      ik:

          alias: [ik_analyzer]

          type: org.elasticsearch.index.analysis.IkAnalyzerProvider

      ik_max_word:

          type: ik

          use_smart: false

      ik_smart:

          type: ik

          use_smart: true
```

或者直接加简单一句

```
index.analysis.analyzer.ik.type : “ik”
```

然后可以编辑```config/ik/IKAnalyzer.cfg.xml```

在里面设置你需要载入的字典文件，刚才拷贝过来的config/ik目录中已经有一些字典文件了。

重启elasticsearch即可。

然后在索引之前，记得设置索引的分词字段等信息，这部分的设置可以在ik的项目页找到。


## 数据同步

### py-mysql-elasticsearch-sync

自己写的一个同步工具，采用python实现，目前用于公司的生产环境数据同步中。[项目主页](https://github.com/zhongbiaodev/py-mysql-elasticsearch-sync)

基本原理是，初始化同步中，利用mysqldump输出xml，在python中进行xml解析，通过es的bulk api提交给es进行索引，增量同步部分，通过解析binlog，将数据的更新提交给es。使用的时候可以自己写多个配置文件，多启动几个脚本，使用不同的配置文件，从而同步不同的表。

下一阶段开发计划是实现同一张表分到不同的索引，以及多个表同步到一个索引中。

### Go-mysql-elasticsearch

以前用的一个go语言实现的同步工具，[项目主页](https://github.com/siddontang/go-mysql-elasticsearch)

特点是可以利用mysql的dump以及binlog来将数据同步进es中，坑的地方在于，似乎目前在一台机器上只能跑一个这个同步工具，同时跑的话，只有一个能拿到dump的数据，其他的卡住不动。这就造成了，数据库中几张表只能一起设置同步，要挂一起挂。

### elasticsearch-jdbc

一个java的同步工具，[项目主页](https://github.com/jprante/elasticsearch-jdbc)

在数据库中执行sql语句，然后把查询到的数据导入es，感觉也比较难用，感觉可以写cronjob定时执行sql，来达到增量更新的效果。

### 其他的一些奇葩方式

因为es官方抽风，把一个叫river的东西给废弃掉了，导致大家没办法开心的导入数据了。传说是可以是用logstash，弄一个插件，来给es导入数据，反正挺蛋疼，还没有具体研究过。

## 性能调优

修改启动脚本，设置一些启动参数

以CentOS下，以RPM包安装的ES为例，

1. 编辑 环境变量文件```/etc/sysconfig/elasticsearch```
  将```ES_HEAP_SIZE```设置为内存的一半
  将```MAX_LOCKED_MEMORY=unlimited```一句取消注释
2. 编辑ES的配置文件```/etc/elasticsearch/elasticsearch.yml```
  将```bootstrap.mlockall: true```一句取消注释
3. 关闭swap（或者修改fstab来永久性关闭）

  ```
  sudo swapoff -a
  ```
4. 重启elasticsearch

另外还有个优化索引性能的：
[https://blog.codecentric.de/en/2014/05/elasticsearch-indexing-performance-cheatsheet/](https://blog.codecentric.de/en/2014/05/elasticsearch-indexing-performance-cheatsheet/)

## 集群
实际使用中，阿里云这类的云主机没有办法使用多播自动发现节点，需要在配置文件中设置为单播，并指定机器，才能成功组成集群

单播设置方法请参考[官方文档中的解释](https://www.elastic.co/guide/en/elasticsearch/guide/1.x/_important_configuration_changes.html#_prefer_unicast_over_multicast)
