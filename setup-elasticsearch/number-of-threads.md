# 线程数
Elasticsearch为各种不同类型的操作大量使用线程池。重要的是，它能够在需要时创建新线程。确保该Elasticsearch用户可以创建的线程数为至少为2048。

在启动Elasticsearch之前，这可以通过root用户执行`ulimit -u 2048`来设置。或在 /etc/security/limits.conf中将nproc设置为2048。