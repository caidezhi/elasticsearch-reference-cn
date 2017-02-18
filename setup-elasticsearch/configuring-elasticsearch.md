# 配置Elasticsearch
Elasticsearch附带了很好的默认值，只需要非常少量的配置。大多数设置都可以通过`集群更改设置`API来修改。

配置文件应该包括节点相关的配置(例如 `node.name` 和`paths`),或者节点加入集群需要的配置，例如`cluster.name` 和 `network.host`。

# 配置文件路径
Elasticsearch 有两个配置文件:
* `elasticsearch.yml` 用于配置Elasticsearch 
* `log4j2.properties` 用于配置Elasticsearch 日志
这些文件都位于config目录，默认路径是 `$ES_HOME/config`。Debian和RPM包会把路径设置为 `/etc/elasticsearch/`。
config目录的路径可以通过修改`path.conf`进行修改,如下:
```
./bin/elasticsearch -Epath.conf=/path/to/my/config/
```

# 配置文件格式
配置文件格式是 `YAML`。下面是修改 data 和 logs目录
```
path:
    data: /var/lib/elasticsearch
    logs: /var/log/elasticsearch
```
配置也能写成扁平风格的
```
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
```

# 环境变量替换
通过`${...}`引用的环境变量将会被替换成环境变量的值，例如:
```
node.name:    ${HOSTNAME}
network.host: ${ES_NETWORK_HOST}
```

# Prompting for settings
对于那些你不想放在配置文件里的配置项，你可以使用`${prompt.text}`或`${prompt.secret}`，同时以前台方式启动Elasticsearch 。`${prompt.secret}`会禁用回显，所以控制台不会显示你的输入;`${prompt.text}`则能让你看到你输入的值。例如:
```
node:
  name: ${prompt.text}
```
当你启动elasticsearch，你会被要求输入实际的值:
```
Enter value for [node.name]:
```
>如果配置里使用了 `${prompt.text}`或`${prompt.secret}`,并以服务或者后台方式启动，elasticsearch将不会启动。

# 设置默认配置
新的默认值可以通过在命令行使用 `default.`前缀指定。这样指定的值会被默认使用除非配置文件里有定义。
例如，如果  elasticsearch像下面这样启动:
```
./bin/elasticsearch -Edefault.node.name=My_Node
```
`node.name` 的值将会是 `My_Node`,除非命令行传递了`es.node.name` 或者配置文件里定义了 `node.name`

# 日志配置
elasticsearch 使用Log4j 2 作为日志系统。Log4j 2可以通过 log4j2.properties文件进行配置。elasticsearch 暴露了一个属性`${sys:es.logs}`,配置文件能够引用这个属性并决定日志文件的路径。这会解析为Elasticsearch 日志文件的前缀。

例如，你的日志目录(`path.logs`) 是`/var/log/elasticsearch`,你的集群命名为 `production`,那么`${sys:es.logs}`会解析为`/var/log/elasticsearch/production`
```
appender.rolling.type = RollingFile 
appender.rolling.name = rolling
appender.rolling.fileName = ${sys:es.logs}.log 
appender.rolling.layout.type = PatternLayout
appender.rolling.layout.pattern = [%d{ISO8601}][%-5p][%-25c] %.10000m%n
appender.rolling.filePattern = ${sys:es.logs}-%d{yyyy-MM-dd}.log 
appender.rolling.policies.type = Policies
appender.rolling.policies.time.type = TimeBasedTriggeringPolicy 
appender.rolling.policies.time.interval = 1 
appender.rolling.policies.time.modulate = true 
```
如果你追加`.gz`或者`.zip` 到 `appender.rolling.filePattern`, 日志文件在归档的时候将会被压缩。

Elasticsearch config目录以及子目录下的 `log4j2.properties`都会被加载并合并，这对暴露额外loggers的插件非常有用。logger包含了java包以及对应的日志级别。appender包含了日志的路径。关于定制日志以及所有支持的appenders 的信息能在 [log4j文档](http://logging.apache.org/log4j/2.x/manual/configuration.html)找到。


