**References of ES documents**

[英文最新版本文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/elasticsearch-intro.html)
[中文文档](https://es.xiaoleilu.com/075_Inside_a_shard/30_Dynamic_indices.html)

## ES install/start（macOS）

#### Install
  
* 命令行：

	```
	curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.3.2-darwin-x86_64.tar.gz
	tar -xvf elasticsearch-7.3.2-darwin-x86_64.tar.gz
	```
* docker (later)
     
#### Start ES Service
 
* from bin directory (```./```: run the executable): 

  ```
  ./bin/elastisearch
  ```  
* start as a daemon (推荐)
  
  ```
  ./bin/elasticsearch -d
  ```
  
  关掉es只需要kill掉pid
  
  ```
  ps aux|grep elasticsearch
  kill -9 <pid>
  ```
* 检查es状态，status=green成功启动
  
   ```
  curl -XGET 'http://localhost:9200/_cluster/health?pretty'
  ```
  以上两种方法都只启动一个ES node的集群。至少需要启动两个es instances, 互为replica备份，否则status为yellow。Ref: [集群健康状态](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_cluster_health.html)
  
  另外启动两个instances，指定数据持久化路径和log路径。
  
  ```
  ./elasticsearch -Epath.data=data2 -Epath.logs=log2 -d
  ./elasticsearch -Epath.data=data3 -Epath.logs=log3 -d
  ```
  
  之后研究scalability的时候会一并研究启动单机多节点，这也是es作为分布式搜索引擎的特色。
  
 
* 其他启动方式(later)
	* start with service 

es的默认配置(port(default=9200)/cluster name/node name/data path/log path等)可以在config/elasticsearch.yml里修改；由于es是基于java开发的, 跑在java JVM里，可以在config/jvm.option里修改xmx的大小。
 
## Index
这个章节主要解释ES索引的知识。涉及定义索引的setting(mapping)，analyzer等。

#### ES存储

参考文章: **Day 7 - Elasticsearch中数据是如何存储的(**<https://elasticsearch.cn/article/6178>)。ES是基于Apache Lucene实现的，底层继承了Lucene数据存储方法。ES对于一个index，主要存储三个部分的信息：

- 原始文档(documents，es中的_source)，主要包含初始写入的fielddata, 格式docId->doc；
- 倒排索引(inverted index), 服务于快速搜索，用FST tree实现，优势为内存占用低；
- 列存数据(docvalues)，用于聚合和排序。

shard(分片)是es中最小的数据存储单元。replica shard(副本分片)是指es会复制每一个primary shard(主分片)，放在另一个data node上面作为备份。某个节点挂掉，主分片丢失时，es会将replica升级为主分片，并且指派新的副本分片。

#### Mapping
     
## restful api && basic query DSL
首先强调下query的读法，这单词100个人有100种错误的读法，反正以“阔”或“Q”开始的读法就不对了，必须是“魁”。这东西也不是特别重要，但效果就像听到有人把SQL一个个字母读出来一样，你感受到的是一股浓浓的乡土气息，这个人再专业也专业不到哪里。我们要注意细节！

  * phrase search
  * autocompletion suggestions
  * similarity search
## kibana
## aggregation
## docker
## scalability
     * clusters/nodes/shards