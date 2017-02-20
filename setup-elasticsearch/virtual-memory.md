# 虚拟内存

Elasticsearch默认使用[hybrid mmapfs / niofs](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-store.html#default_fs)目录存储其索引。操作系统默认的MMAP计数的限制很可能太低，这可能会导致内存不足异常。

在Linux上，你可以通过root用户运行以下命令：
```
sysctl -w vm.max_map_count=262144
```

要永久设置此值，更新/etc/sysctl.conf 中vm.max_map_count值。重启后验证，运行 `sysctl vm.max_map_count`。

在RPM和Debian软件包将自动配置此设置。没有进一步的配置是必需的。
