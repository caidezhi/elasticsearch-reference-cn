# 通过jvm.options设置JVM堆

默认情况下，Elasticsearch让JVM使用最小和最大值为2GB的堆内存。
当应用到生产环境，配置堆内存大小来保证 Elasticsearch 有足够的堆空间，是重要的。

Elasticsearch会根据在jvm.options 中Xms(最小堆)和Xmx（最大堆）
的设置来分配整个堆。

这些值得设定取决于服务器的内存大小。下面是一些最佳实践：

* 把最小堆(Xms)和最大堆(Xmx)设置成相等。
* 可用的堆空间越大，Elasticsearch用于缓存的内存就越多。但太大的堆会导致比较长的GC（垃圾回收）停顿。
* Xmx最大不要超过物理内存的50%，这可以保证有足够的内存用于内核文件系统缓存。
* 不要把Xmx设置为压缩对象指针的内存限额之上。具体的值大约是32GB。你可以通过日志来验证这个限制，如下
```
heap size [1.9gb], compressed ordinary object pointers [true]
```
* 尽量设置为零基压缩的阈值内；在多数系统上26GB是安全的，但在一些系统上也能大到30GB。用JVM参数-`XX:+UnlockDiagnosticVMOptions -XX:+PrintCompressedOopsMode` 启动Elasticsearch, 你可以在日志里找下面的信息来验证：`heap address: 0x000000011be00000, size: 27648 MB, zero based Compressed Oops`显示零基压缩被启用了

下面是通过jvm.options文件设置堆内存的例子:

```
-Xms2g 
-Xmx2g
```

通过环境变量来设置堆内存也是可以的。这可以通过注释在jvm.options文件中的Xms和Xms设置，并通过`ES_JAVA_OPTS`设置这些值做到。

```
ES_JAVA_OPTS="-Xms2g -Xmx2g" ./bin/elasticsearch 
ES_JAVA_OPTS="-Xms4000m -Xmx4000m" ./bin/elasticsearch
```