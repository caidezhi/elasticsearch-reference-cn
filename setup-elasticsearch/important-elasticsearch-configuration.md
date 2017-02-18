# 重要的Elasticsearch配置
Elasticsearch需要很少的配置，但在投入生产环境之前，有些配置项需要也有必要人工设置。
* path.data and path.logs
* cluster.name
* node.name
* bootstrap.memory_lock
* network.host
* discovery.zen.ping.unicast.hosts
* discovery.zen.minimum_master_nodes

## path.data和path.logs
如果您正在使用`.zip`或`.tar.gz`包，data和logs 目录是`$ES_HOME`的子文件夹。如果这些重要的文件夹都留在它们的默认位置，当升级Elasticsearch到新版本时，它们就有被删除的风险。

在生产环境中使用，你肯定会想改变数据和日志文件夹的位置：
```
path:
  logs: /var/log/elasticsearch
  data: /var/data/elasticsearch
```
RPM 和Debian 发行版都会为data和logs使用定制的路径。
`path.data`配置可以设置成多个路径,在这种情况下，所有的路径将被用于存储数据（尽管属于单个子库中的文件都将被存储在相同的数据路径上）：
```
path:
  data:
    - /mnt/elasticsearch_1
    - /mnt/elasticsearch_2
    - /mnt/elasticsearch_3
```

## cluster.name
一个节点只能加入cluster.name与其他节点相同的集群。默认名称为elasticsearch，但你应该将其更改为一个合适的名称，能体现
集群的用途。
```
cluster.name: logging-prod
```
请确保不要在不同的环境相同的群集名称，否则节点可能会加入错误的集群。

## node.name
默认情况下，Elasticsearch将会取随机生成的UUID的前7位作为节点名称。注意，节点ID会被持久化，且当节点重新启动，缺省节点名也不会改变。

起一个有意义的名称是有好处的：
```
node.name: prod-data-2
```
`node.name` 也能被设置为服务器的 HOSTNAME 
```
node.name: ${HOSTNAME}
```
