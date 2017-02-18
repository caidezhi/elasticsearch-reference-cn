# 安装Elasticsearch

## 使用`.zip`,`.tar.gz`安装Elasticsearch

Elasticsearch提供`.zip`和`.tar.gz`格式的安装包。这些安装包是跨平台的，也是体验Elasticsearch最容易的一种方式。

最新的稳定版Elasticsearch可以在这里找到[下载Elasticsearch](https://www.elastic.co/downloads/elasticsearch)。其他的版本可以在这里找到[过去的版本](https://www.elastic.co/downloads/past-releases)

> Elasticsearch需要java 8或更新的版本。使用 [oracle官方发行版](http://www.oracle.com/technetwork/java/javase/downloads/index.html)或者开源的发行版如[OpenJDK](http://openjdk.java.net/)

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

    kill `cat pid`

### 从命令行配置 elasticsearch

默认情况下elasticsearch 从`$ES_HOME/config/elasticsearch.yml`加载配置。配置文件格式说明能在[配置elasticsearch ]()找到。

配置文件中的任何设置，都能通过 `-E` 在命令行设置。

```
./bin/elasticsearch -d -Ecluster.name=my_cluster -Enode.name=node_1
```

> 通常集群相关的设置\(像集群名字\)应该添加到`elasticsearch.yml`配置文件，任何节点相关的设置，例如节点名字应该从命令行传递。

### `.zip`和`.tar.gz`包的目录结构

`.zip`和`.tar.gz` 包是完整的。所有应该包含在`$ES_HOME`中的目录和文件都会在解压缩创建。

这使得使用elasticsearch 非常方便，因为你不用额外创建任何目录同时要卸载elasticsearch 时只需要简单的把`$ES_HOME`目录删掉。还是建议大家修改默认的配置目录，数据目录还有日志目录以便以后不会删掉任何重要的数据。

* **home**: Elasticsearch 主目录或 $ES\_HOME。默认值为解压目录。
* **bin** : 二进制脚本包括用于启动节点的 `elasticsearch` 还有安装插件的`elasticsearch-plugin`。默认值为 `$ES_HOME/bin`
* **conf**:包括`elasticsearch.yml`在内的配置文件。默认值为 `$ES_HOME/config`, 配置参数为 `path.conf`
* **data**: 每个索引和分片防止数据文件的目录。 默认值为`$ES_HOME/data`
  配置参数为`path.data`
* **logs**: 日志文件目录。默认值为`$ES_HOME/logs`配置参数为`path.logs`
  -**plugins**: 插件文件，每个插件都放置在子目录。默认值为`$ES_HOME/plugins`
  -**repo**: 共享文件系统仓库。配置参数为`path.repo`
  -**script**: 脚本文件目录，默认值为`$ES_HOME/scripts` 配置参数为`path.scripts`

## 使用Docker安装Elasticsearch

Docker镜像可以通过以下命令获得

```
docker pull docker.elastic.co/elasticsearch/elasticsearch:5.2.1
```

### 从命令行运行elasticsearch

#### 开发模式

elasticsearch 能通过以下命令快速启动，用于开发或者测试。

```
docker run -p 9200:9200 -e "http.host=0.0.0.0" -e "transport.host=127.0.0.1" docker.elastic.co/elasticsearch/elasticsearch:5.2.1
```

#### 生产模式

* linux

修改 `/etc/sysctl.conf` 中的 `vm_map_max_count`

```
$ grep vm.max_map_count /etc/sysctl.conf
vm.max_map_count=262144
```

* OSX [Docker for Mac](https://docs.docker.com/engine/installation/mac/#/docker-for-mac)

设置xhyve 虚拟机中的`vm_max_map_count`

```
$ screen ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty
```

使用root登录，跟linux上一样配置`sysctl`

```
sysctl -w vm.max_map_count=262144
```

下面的例子会启动一个有两个节点的Elasticsearch 集群。请使用[docker-compose.yml](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#docker-prod-cluster-composefile)，并使用如下命令：

```
docker-compose up
```

节点 elasticsearch1 会在 localhost:9200 上监听，elasticsearch2 会通过Docker网络与elasticsearch1 通讯

这个例子会使用Docker 命名卷，也就是esdata1和esdata2  
docker-compose.yml:

```
version: '2'
services:
  elasticsearch1:
    image: docker.elastic.co/elasticsearch/elasticsearch:5.2.1
    container_name: elasticsearch1
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    mem_limit: 1g
    cap_add:
      - IPC_LOCK
    volumes:
      - esdata1:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - esnet
  elasticsearch2:
    image: docker.elastic.co/elasticsearch/elasticsearch:5.2.1
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - "discovery.zen.ping.unicast.hosts=elasticsearch1"
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    mem_limit: 1g
    cap_add:
      - IPC_LOCK
    volumes:
      - esdata2:/usr/share/elasticsearch/data
    networks:
      - esnet

volumes:
  esdata1:
    driver: local
  esdata2:
    driver: local

networks:
  esnet:
    driver: bridge
```

要停止集群，请使用 `docker-compose down` ,数据卷会保存数据，所以我们可以使用 `docker-compose up` 启动之前的集群。要销毁  
集群还有数据卷只需使用 `docker-compose down -v`。

#### 查看集群状态

```
curl -u elastic http://127.0.0.1:9200/_cat/health
Enter host password for user 'elastic':
1472225929 15:38:49 docker-cluster green 2 2 4 2 0 0 0 0 - 100.0%
```

日志会输出到控制台并被预先配置好的docker日志驱动处理。默认情况下，你可以通过`docker logs` 访问日志

### 用docker配置elasticsearch

Elasticsearch 从`/usr/share/elasticsearch/config/` 加载配置文件。  
关于配置文件的文档能 [配置Elasticsearch ](https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html)和 [设置JVM参数](https://www.elastic.co/guide/en/elasticsearch/reference/current/setting-system-settings.html#jvm-options)找到。

镜像提供了好几种方式来配置Elasticsearch ,可以使用自定义的配置文件，例如:   
`elasticsearch.yml`,也可以使用环境变量

#### A.通过docker环境变量设置参数

例如，通过传递`-e "cluster.name=mynewclustername"` 给 `docker run` 命令来定义集群名字。

#### B.Bind-mounted配置

创建你自己的配置文件并挂载到镜像对应的文件上。例如 运行`docker run`命令是挂载上`custom_elasticsearch.yml`

```
-v full_path_to/custom_elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
```

#### C.定制化镜像

在一些环境下准备一个包含个性化设置的镜像会更合理。Dockerfile 可以做到这点：

```
FROM docker.elastic.co/elasticsearch/elasticsearch:5.2.1
ADD elasticsearch.yml /usr/share/elasticsearch/config/
USER root
chown elasticsearch:elasticsearch config/elasticsearch.yml
USER elasticsearch
```

你可以编译并运行镜像

```
docker build --tag=elasticsearch-custom .
docker run -ti -v /usr/share/elasticsearch/data elasticsearch-custom
```

#### D.覆盖镜像默认 CMD

参数能作为命令行参数传递给elasticsearch进程

```
docker run <various parameters> bin/elasticsearch -Ecluster.name=mynewclustername
```



