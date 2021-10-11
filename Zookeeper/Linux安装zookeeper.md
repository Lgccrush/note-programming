### Linux安装zookeeper

安装前首先当然是下载安装包了。我们进入，zookeeper官网： https://zookeeper.apache.org/releases.html

前提：由于zookeeper是使用java语言开发的，所以，在安装zookeeper之前务必先在本机安装配置好java环境！

1，找到下载链接，然后在linux中先下载所需安装包（我这边下载的是3.4.9版本）：

```shell
 wget https://archive.apache.org/dist/zookeeper/zookeeper-3.4.9/zookeeper-3.4.9.tar.gz
```

2，解压

```sh
tar -zxvf zookeeper-3.4.9.tar.gz
```

3，配置conf文件

进入到安装目录的…/conf目录下,可以看到这里有个zoookeeper给我们的一个样例配置文件：zoo_sample.cfg，我们在配置我们自己的zk时，需要做的就是将这个文件复制一份，并命名为：zoo.cfg，然后在zoo.cfg中修改自己的配置即可。

```sh
cp zoo_sample.cfg zoo.cfg
vim zoo.cfg
```

zoo.cfg的相关配置项其实并不多，这边各个配置项的详细说明如下：

```sh
# zookeeper内部的基本单位，单位是毫秒，这个表示一个tickTime为2000毫秒，在zookeeper的其他配置中，都是基于tickTime来做换算的
tickTime=2000

#集群中的follower服务器(F)与leader服务器(L)之间 初始连接 时能容忍的最多心跳数（tickTime的数量）。
initLimit=10

#syncLimit：集群中的follower服务器(F)与leader服务器(L)之间 请求和应答 之间能容忍的最多心跳数（tickTime的数量）
syncLimit=5

# 数据存放文件夹，zookeeper运行过程中有两个数据需要存储，一个是快照数据（持久化数据）另一个是事务日志
dataDir=/tmp/zookeeper

# 客户端访问端口
clientPort=2181
```

4，配置环境变量

```sh
vim /etc/profile

export ZOOKEEPER_HOME=/zookeeper安装目录/zookeeper-3.4.9
export PATH=$ZOOKEEPER_HOME/bin:$PATH
export PATH

source /etc/profile #刷新环境变量
```

5，启动服务

```sh
zkServer.sh start
```

可以查看下启动状态：

```sh
zkServer.sh status
```

客户端连接

```sh
zkCli.sh
```