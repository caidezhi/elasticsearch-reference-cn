# 禁用交换

大多数操作系统会尽量使用内存为文件系统缓存，并急切地换出不用的应用程序内存。这会导致JVM堆部分被交换到磁盘。

交换会影响节点的性能和稳定性，应该不惜一切代价避免。它可能导致的垃圾收集持续了几分钟 而不是毫秒，并可能导致节点缓慢，甚至导致退出集群。

有三种方法来禁用交换：

## 启用bootstrap.memory_lock

第一种选择是使用在Linux / Unix系统的`mlockall`或在Windows上的`VirtualLock`，要尽量锁定RAM中的进程地址空间，防止任何Elasticsearch内存被换出。这可以通过往config/elasticsearch.yml文件添加一行配置做到：
```
bootstrap.memory_lock: true
```

启动Elasticsearch后，可以从下面这个请求的输出中检查mlockall的值来查看此设置是否成功：

```
curl -XGET 'localhost:9200/_nodes?filter_path=**.mlockall&pretty'
```

如果你看到mlockall的值是false，那么就意味着该mlockall 请求失败。你还将在日志中看到`Unable to lock JVM Memory`。

最可能的原因，在Linux / Unix系统，是运行Elasticsearch用户没有权限锁定内存。可以通过的方式来授权：

`.zip`和 `.tar.gz`
在启动Elasticsearch前，通过`ulimit -l unlimited`或者在 `/etc/security/limits.conf` 中把memlock设为`unlimited`

RPM and Debian
在[系统配置文件](https://www.elastic.co/guide/en/elasticsearch/reference/current/setting-system-settings.html#sysconfig)中,把 `MAX_LOCKED_MEMORY` 设为`unlimited`

使用 `systemd` 的系统
在[systemd配置](https://www.elastic.co/guide/en/elasticsearch/reference/current/setting-system-settings.html#systemd)中，把`LimitMEMLOCK`设为`infinity` 

另一个mlockall 出错的原因可能是临时目录以 `noexec`参数挂载时。
这可以通过在`ES_JAVA_OPTS`环境变量中指定一个新的临时目录来解决。

```
export ES_JAVA_OPTS="$ES_JAVA_OPTS -Djava.io.tmpdir=/path/to/temp/dir"
./bin/elasticsearch
```	
或者在 `jvm.options`配置文件中添加jvm参数。


## 禁用所有交换文件
另外一个选择是完全禁用交换。通常Elasticsearch是服务器上运行的唯一服务，并且其内存使用由JVM选项控制。所以应该没有必要启用交换。

在Linux系统中，你可以通过`sudo swapoff -a`命令暂时禁用交换。要永久禁用它，你将需要编辑的`/etc/fstab`文件，并注释掉所有包含单词swap的行。

在Windows上，可以完全通过禁用页面文件来实现`System Properties → Advanced → Performance → Advanced → Virtual memory`。

## 配置 swappiness
在Linux系统可用的另一种选择是，确保 sysctl值 vm.swappiness被设定为1。这减少了内核的交换，这使得正常情况下不会发生交换，同时仍然允许系统在紧急情况下进行交换。
