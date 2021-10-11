### Linux安装kafka

```
什么是消息队列（Message Queue）？

消息(Message)

网络中的两台计算机或者两个通讯设备之间传递的数据。例如说：文本、音乐、视频等内容。

队列(Queue)

一种特殊的线性表（数据元素首尾相接），特殊之处在于只允许在首部删除元素和在尾部追加元素。入队、出队。

消息队列(MQ)

消息+队列，保存消息的队列。消息的传输过程中的容器；主要提供生产、消费接口供外部调用做数据的存储和获取。
```
[kafka下载地址](https://kafka.apache.org/downloads)

```sh
wget http://mirrors.hust.edu.cn/apache/kafka/2.0.0/kafka_2.12-2.0.0.tgz #下载
tar -xzvf kafka_2.12-2.0.0.tgz #解压
```
修改配置
```sh
vim config//server.properties 
# 允许外部端口连接                                           
listeners=PLAINTEXT://0.0.0.0:9092  
# 外部代理地址                                                
advertised.listeners=PLAINTEXT://服务器ip:9092
不修改启动报错
onnection to node -1 could not be established. Broker may not be available.
```

启动

```sh
安装目录下
 nohup ./bin/kafka-server-start.sh config/server.properties &
 或者
 ./bin/kafka-server-start.sh -deamon config/server.properties
```

停止

```sh
./bin/kafka-server-stop.sh config/server.properties 
```
查看生产者消息 bin目录下
```sh
./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic名称 --from-beginning
```
kafka报错
```sh
java.lang.IllegalArgumentException: requirement failed: No jmx port but jmx polling enabled!
启动kafka加上  JMX_PORT=9999
或者
在kafka-server-start.sh添加export JMX_PORT=9999
if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
    export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G"
    export JMX_PORT="9999"
```

### Linux 安装kafka Manager

解压kafka-manager-2.0.0.2

```shll
 upzip kafka-manager-2.0.0.2.zip 
```

修改conf/application.conf文件，特别是kafka-manager.zkhosts的配置

```shll
  kafka-manager.zkhosts="localhost:2181"
```

运行kafka manager　注意：默认启动端口为9000。

```sh
nohup bin/kafka-manager -Dconfig.file=conf/application.conf &  #(后台运行)
```

指定端口

```sh
nohup bin/kafka-manager  -Dconfig.file=/kafka安装路径/conf/application.conf -Dhttp.port=10000 &
```

停止kafkaManager
```sh
lsof -i:10000
kill -9 PID
rm RUNNING_PID 

```