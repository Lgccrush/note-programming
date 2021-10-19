一、kafka 全局消息过期时间设置
1） 进入kafka配置文件夹     

```sh
cd  /opt/kafka/config/
```

默认的是在server.properties 文件里面

2）需要修改和配置项如下：

```properties
log.retention.hours=168 (配置该参数即可)
log.cleanup.policy=delete （默认，可不配置）
```

3） 修改配置后重启kafka服务生效，kafka默认的消息过期时间为168h(7天)

该种设置消息过期时间的优点是可以对所有topic全部生效，缺点是需要重启kafka服务，造成服务短暂的不可用！

二、针对特定topic设置过期时间
实际上，可以不停服针对kafka中数据量较大的topic可以单独设置过期时间，不受全局过期时间的限制，而且不需要重启kafka

下面以数据较多的 mytopic为例， 全局默认消息过期时间为7天，现在将其调整为1天

1） 进入kafka 的安装目录

```sh
cd /opt/kafka/bin
```

2) 执行设置命令

```sh
./kafka-configs.sh --zookeeper localhost:2181 --alter --entity-name mytopic--entity-type topics --add-config retention.ms=86400000
```

时间设置一天 86400000ms = 1天

三、kafka过期消息删除过程
      kafka消息首先由用户设定一个或多个partition，每个partition中kafka会根据消息量来逐步建立多个segment存储消息，每个segment的大小由配置项进行设定，比如这里

```properties
log.segment.bytes=1073741824 【1GB】
```

kafka至少会保留1个工作segment保存消息。消息量超过单个文件存储大小就会新建segment，比如消息量为2.6GB, 就会建立3个segment。kafka会定时扫描非工作segment，将该文件时间和

设置的topic过期时间进行对比，如果发现过期就会将该segment文件（具体包括一个log文件和两个index文件）打上.deleted 的标记，如下所示：

-rw-r--r-- 1 root root 1073740353 Nov 13 03:02 00000000000108550131.log.deleted
-rw-r--r-- 1 root root 526304 Nov 13 03:02 00000000000108550131.index.deleted
-rw-r--r-- 1 root root 697704 Nov 13 03:02 00000000000108550131.timeindex.deleted

最后kafka中会有专门的删除日志定时任务过来扫描，发现.deleted文件就会将其从磁盘上删除，释放磁盘空间，至此kafka过期消息删除完成。

       可以看出，kafka删除消息是以segment为维度的，而不是以具体的一条条消息为维度。一个segment包含了一段时期的全部消息并存储在一个文件中，比如上文提到的 00000000000108550131.log

删除时是一次性把这个过期的文件包含所有消息全部删除，效率非常高。可以设想如果是先判断一条条的消息时间是否过期再一条条的执行删除，将十分影响kafka的性能和效率，频繁擦除磁盘，对硬盘性能也有较大影响！

       所以如果消息量不多没有超过一个segment的存储容量，由于kafka至少要保留一个segment用于存取消息，所以也不会去删除里面过期的消息。实际上，也存在着设置了消息7天过期，但是kafka里面仍存在着10天前的数据，这就是由kafka的删除特性决定的。

我们删除过期消息的目的是为了释放磁盘空间，既然消息很少没有突破一个segment的容量，那么即使多保留几天的旧消息又何妨，又不怎么占用磁盘空间。然而这种删除策略的设计换来的却是大消息量的topic整块消息删除的高性能和高效率！

不得不说这种设计理念非常先进！

