# 安装
在写这篇文章时Elasticsearch运行需要Java 8，建议使用Oracle JDK版本1.8.0_73。Java安装因平台而异，所以我们不会在这里讲述这些细节。Oracle推荐的安装文档可以在[Oracle的网站](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)上找到。我只想说，在安装之前Elasticsearch，首先请通过运行下面的命令检查你的Java版本（然后安装/如果需要相应地升级）：

```shell
java -version
echo JAVA_HOME
```
一旦我们安装了java，我们就可以下载并运行Elasticsearch。 二进制包可从[www.elastic.co/downloads](www.elastic.co/downloads)下载，在这里你也能下载到以前发布的版本。每次版本发布，你都可以在 zip,tar,DEB,RPM类型的包中选择下载。为了简单起见，我们使用tar包。

用下面的命令下载Elasticsearch 5.2.1(windows 用户可以选择zip包)
```shell
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.1.tar.gz
```
用下面的命令解压
```shell
tar -xvf elasticsearch-5.2.1.tar.gz
```
你会在当前目录看多了一系列文件，让我们进入bin目录：
```shell
cd elasticsearch-5.2.1/bin
```
现在我们可以启动节点和集群
```shell
./elasticsearch
```
如果一切顺利，你应该看到类似下面一些列日志
```shell
[2016-09-16T14:17:51,251][INFO ][o.e.n.Node               ] [] initializing ...
[2016-09-16T14:17:51,329][INFO ][o.e.e.NodeEnvironment    ] [6-bjhwl] using [1] data paths, mounts [[/ (/dev/sda1)]], net usable_space [317.7gb], net total_space [453.6gb], spins? [no], types [ext4]
[2016-09-16T14:17:51,330][INFO ][o.e.e.NodeEnvironment    ] [6-bjhwl] heap size [1.9gb], compressed ordinary object pointers [true]
[2016-09-16T14:17:51,333][INFO ][o.e.n.Node               ] [6-bjhwl] node name [6-bjhwl] derived from node ID; set [node.name] to override
[2016-09-16T14:17:51,334][INFO ][o.e.n.Node               ] [6-bjhwl] version[5.2.1], pid[21261], build[f5daa16/2016-09-16T09:12:24.346Z], OS[Linux/4.4.0-36-generic/amd64], JVM[Oracle Corporation/Java HotSpot(TM) 64-Bit Server VM/1.8.0_60/25.60-b23]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [aggs-matrix-stats]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [ingest-common]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [lang-expression]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [lang-groovy]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [lang-mustache]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [lang-painless]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [percolator]
[2016-09-16T14:17:51,968][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [reindex]
[2016-09-16T14:17:51,968][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [transport-netty3]
[2016-09-16T14:17:51,968][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [transport-netty4]
[2016-09-16T14:17:51,968][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded plugin [mapper-murmur3]
[2016-09-16T14:17:53,521][INFO ][o.e.n.Node               ] [6-bjhwl] initialized
[2016-09-16T14:17:53,521][INFO ][o.e.n.Node               ] [6-bjhwl] starting ...
[2016-09-16T14:17:53,671][INFO ][o.e.t.TransportService   ] [6-bjhwl] publish_address {192.168.8.112:9300}, bound_addresses {{192.168.8.112:9300}
[2016-09-16T14:17:53,676][WARN ][o.e.b.BootstrapCheck     ] [6-bjhwl] max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]
[2016-09-16T14:17:56,731][INFO ][o.e.h.HttpServer         ] [6-bjhwl] publish_address {192.168.8.112:9200}, bound_addresses {[::1]:9200}, {192.168.8.112:9200}
[2016-09-16T14:17:56,732][INFO ][o.e.g.GatewayService     ] [6-bjhwl] recovered [0] indices into cluster_state
[2016-09-16T14:17:56,748][INFO ][o.e.n.Node               ] [6-bjhwl] started
```
我们可以看到我们的节点命名为“6-bjhwl”（你可能会看到一个不同的名字）已经启动，并且在集群中被选为主节点。你可能还不明白什么是主节点，别担心，重要的是，我们已经在一个集群中启动了一个节点。


像之前提到的,我们可以覆盖集群或者节点的名字。我们可以在启动Elasticsearch的时候通过命令行来指定，如下：
```shell
./elasticsearch -Ecluster.name=my_cluster_name -Enode.name=my_node_name
```
请注意包含"http"的那一行日志，我们通过HTTP地址(192.168.8.112)和端口(9200)来访问节点。默认情况下，Elasticsearch使用9200来提供对其REST API的访问。如果有必要，这个端口是可以配置的。



