# 安装Elasticsearch

## 使用`.zip`,`.tar.gz`安装Elasticsearch

Elasticsearch提供`.zip`和`.tar.gz`格式的安装包。这些安装包是跨平台的，也是体验Elasticsearch最容易的一种方式。

最新的稳定版Elasticsearch可以在这里找到[下载Elasticsearch](https://www.elastic.co/downloads/elasticsearch)。其他的版本可以在这里找到[过去的版本](https://www.elastic.co/downloads/past-releases)

>Elasticsearch需要java 8或更新的版本。使用 [oracle官方发行版](http://www.oracle.com/technetwork/java/javase/downloads/index.html)或者开源的发行版如[OpenJDK](http://openjdk.java.net/)

### 下载安装`.zip`安装包

Elasticsearch v5.2.1 可以通过下面的命令下载和安装
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.1.zip
sha1sum elasticsearch-5.2.1.zip 
unzip elasticsearch-5.2.1.zip
cd elasticsearch-5.2.1/
```

### 下载安装`.tar.gz`安装包

Elasticsearch v5.2.1 可以通过下面的命令下载和安装
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.1.tar.gz
sha1sum elasticsearch-5.2.1.tar.gz 
tar -xzf elasticsearch-5.2.1.tar.gz
cd elasticsearch-5.2.1/
```

