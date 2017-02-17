# 安装Elasticsearch

## 使用`.zip`,`.tar.gz`安装Elasticsearch

Elasticsearch提供`.zip`和`.tar.gz`格式的安装包。这些安装包是跨平台的，也是体验Elasticsearch最容易的一种方式。

最新的稳定版Elasticsearch可以在这里找到[下载Elasticsearch](https://www.elastic.co/downloads/elasticsearch)。其他的版本可以在这里找到[过去的版本](https://www.elastic.co/downloads/past-releases)

>Elasticsearch需要java 8或更新的版本。使用 [oracle官方发行版](http://www.oracle.com/technetwork/java/javase/downloads/index.html)或者开源的发行版如[OpenJDK](http://openjdk.java.net/)

### 下载安装`.zip`包

Elasticsearch v5.2.1 可以通过下面的命令下载和安装
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.1.zip
sha1sum elasticsearch-5.2.1.zip 
unzip elasticsearch-5.2.1.zip
cd elasticsearch-5.2.1/
```

### 下载安装`.tar.gz`包

Elasticsearch v5.2.1 可以通过下面的命令下载和安装
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.1.tar.gz
sha1sum elasticsearch-5.2.1.tar.gz 
tar -xzf elasticsearch-5.2.1.tar.gz
cd elasticsearch-5.2.1/
```
### 从命令行运行Elasticsearch 
elasticsearch 能从命令行启动
```
./bin/elasticsearch
```
默认情况下elasticsearch以前台模式运行，将log打印到标准输出，还有通过
`ctrl+c`停止。
### 检查Elasticsearch
你能通过发送http请求到`localhost`上的9200端口来测试elasticsearch 节点是否正在运行
```
curl -XGET 'localhost:9200/?pretty'
```
你应该能得到类似下面的响应
```
{
  "name" : "Cp8oag6",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "AT69_T_DTp-1qgIJlatQqA",
  "version" : {
    "number" : "5.2.1",
    "build_hash" : "f27399d",
    "build_date" : "2016-03-30T09:51:41.449Z",
    "build_snapshot" : false,
    "lucene_version" : "6.4.1"
  },
  "tagline" : "You Know, for Search"
}
```
打印到`stdout`的日志能通过传递`-q`或`--quiet`命令行参数来禁止

### 作为守护进程运行
`-d`参数能使elasticsearch 运行为守护进程，`-p`参数能记录进程ID
```
./bin/elasticsearch -d -p pid
```
日志会在`$ES_home/logs`目录中找到

要关闭elasticsearch ，可以用如下命令
```
kill `cat pid`
```

### 从命令行配置 elasticsearch 

默认情况下elasticsearch 从`$ES_HOME/config/elasticsearch.yml`加载配置。配置文件格式说明能在[配置elasticsearch ]()找到。

配置文件中的任何设置，都能通过 `-E` 在命令行设置。
```
./bin/elasticsearch -d -Ecluster.name=my_cluster -Enode.name=node_1
``` 
>通常集群相关的设置(想集群名字)应该添加到`elasticsearch.yml`配置文件，任何节点相关的设置，例如节点名字应该从命令行传递。


