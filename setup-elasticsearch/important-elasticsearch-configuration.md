#重要的Elasticsearch配置

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

## bootstrap.memory_lock
JVM 不会被交换到磁盘对节点的健康尤为重要。将 `bootstrap.memory_lock` 设置为`true`是实现这点的一个方法。

要让这个设置生效，还有其他的系统设置需要修改。关于怎么正确地设置内存锁定，请参照 [Enable bootstrap.memory_lock](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration-memory.html#mlockall) 。

## network.host
默认情况下 Elasticsearch只绑定到回环地址,例如:`127.0.0.1`和`[::1]`。这对在机器上跑起一个开发节点已经足够了。

为了与其他机器上的节点通讯并组成集群，你的节点必须绑定到非回环地址。有很多的网络相关的设置，通常情况下你需要配置 `network.host`:
```
network.host: 192.168.1.10
```
`network.host` 可以设置为一些特殊的值例如：`_local_`, `_site_`, `_global_`和修饰符像 `:ip4`和`:ip6`, 详细的信息可以在 [Special values for network.host](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-network.html#network-interface-values) 找到。

## discovery.zen.ping.unicast.hosts
在不更改任何网络配置的情况下，elasticsearch 会绑定到回环地址，并扫描9300到9350之前的端口，尝试连接上同台机器上的其他节点。这使得不用做任何配置就可以自动组成集群。

当要与其他机器上的节点组成集群时，必须提供一个其他集群中其他节点组成的列表。如下配置：
```
discovery.zen.ping.unicast.hosts:
   - 192.168.1.10:9300
   - 192.168.1.11 
   - seeds.mydomain.com
```

## discovery.zen.minimum_master_nodes

为了防止数据丢失，配置 `discovery.zen.minimum_master_nodes` 是至关重要的。这样每个master候选节点才能知道组成集群需要的最少的master节点数量。

没有这个配置，当集群遇到了网络问题，便可能分裂成两个独立的集群——脑裂——这可能会导致数据丢失。更详尽的解释在 [Avoiding split brain with minimum_master_nodesedit](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-node.html#split-brain)。

为了避免脑裂，这个参数应该设置如下：
```
(master_eligible_nodes / 2) + 1
```
也就是说，如果有3个master候选节点，那么最小的master节点数量应该设置为(3 / 2) + 1 or 2：
```
discovery.zen.minimum_master_nodes: 2
```
