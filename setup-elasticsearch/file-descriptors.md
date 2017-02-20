# 文件描述符
Elasticsearch使用了大量的文件描述符或文件句柄。耗尽文件描述符可能是灾难性的，将很可能导致数据丢失。确保增加运行Elasticsearch用户的文件描述符的数量限制至65536或更高。

对于`.zip`和`.tar.gz`，在启动Elasticsearch之前，用root用户设定`ulimit -n 65536`，或者在 /etc/security/limits.conf 中设置nofile为65536。

RPM和Debian软件包默认文件描述符的最大数量为65536，不需要进一步的配置。

您可以用节点状态API检查 max_file_descriptors：
```
curl -XGET 'localhost:9200/_nodes/stats/process?filter_path=**.max_file_descriptors&pretty'
```