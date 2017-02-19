# 重要的系统配置
理想情况下，Elasticsearch应该在服务器上单独运行，并使用所有可用资源。为了做到这一点，你需要配置操作系统，以允许Elasticsearch可以使用的资源超过默认的配额。

在应用到生产环境之前，需要做如下配置:

* [设置JVM堆内存大小](set-jvm-heap-sizes.md)
* [禁用内存交换](disable-swapping.md)
* [增加文件描述符](file-descriptors.md)
* [保证足够的虚拟内存](virtual-memory.md)
* [保证足够的线程](number-of-threads.md)

## 开发模式 vs 生产模式
默认情况下，Elasticsearch假定你在开发模式下工作。如果上述任何设置没有正确配置，警告将被写入日志文件，但您将能够启动和运行Elasticsearch节点。

只要你修改网络设置像network.host，Elasticsearch便认为是生产环境，将上述警告升级到异常。这些异常会阻止你的Elasticsearch节点启动。这是一个重要的安全措施，以确保您不会因为没有正确配置服务器而丢失数据。