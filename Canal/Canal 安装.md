### Canal 安装

[Canal地址](https://github.com/alibaba/canal)

下载canal.deployer 

解压

```sh
 tar -zxvf canal.deployer-1.1.5.tar.gz 
```

配置conf目录下的canal.properties配置文件

```properties
canal.zkServers =localhost:2181
# tcp, kafka, RocketMQ 这里选择kafka模式
canal.serverMode = kafka
# 解析器的线程数，打开此配置，不打开则会出现阻塞或者不进行解析的情况
canal.instance.parser.parallelThreadSize = 16
# 配置MQ的服务地址，这里配置的是kafka对应的地址和端口 kafka为bootstrap.servers rocketMQ中为nameserver列表
kafka.bootstrap.servers  = 127.0.0.1:9092
# 配置instance，在conf目录下要有example同名的目录，可以配置多个
canal.destinations = example
```

然后配置instance，找到/conf/example/instance.properties配置文件：

```properties
## mysql serverId , v1.0.26+ will autoGen(自动生成，不需配置)
# canal.instance.mysql.slaveId=0

# position info
canal.instance.master.address=127.0.0.1:3306
# 在Mysql执行 SHOW MASTER STATUS;查看当前数据库的binlog
canal.instance.master.journal.name=mysql-bin.000006
canal.instance.master.position=4596
# 账号密码
canal.instance.dbUsername=root
canal.instance.dbPassword=root
canal.instance.connectionCharset = UTF-8
#MQ队列名称
canal.mq.topic=kafka的topic名称
#单队列模式的分区下标
canal.mq.partition=0
#配置过滤表
canal.instance.filter.regex=.*\\..*
```

启动

```sh
sh bin/startup.sh
```

启动报错

```sh
There is insufficient memory for the Java Runtime Environment to continue.
修改startup.sh中的如下参数：
可以根据自己的机器适当调整 -Xms -Xmx -Xmn 参数
if [ -n "$str" ]; then
        JAVA_OPTS="-server -Xms256m -Xmx256m -Xmn256m -XX:SurvivorRatio=2 -XX:PermSize=96m -XX:MaxPermSize=256m -Xss256k -XX:-UseAdaptiveSizePolicy -XX:MaxTenuringThreshold=15 -XX:+DisableExplicitGC -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled -XX:+UseCMSCompactAtFullCollection -XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly -XX:+HeapDumpOnOutOfMemoryError"
else
        JAVA_OPTS="-server -Xms256m -Xmx256m -XX:NewSize=256m -XX:MaxNewSize=256m -XX:MaxPermSize=128m "
fi

```

查看日志

a.查看 logs/canal/canal.log

```sh
vi logs/canal/canal.log
```


b. 查看instance的日志：

```sh
vi logs/example/example.log
```

关闭
sh bin/stop.sh

问题 数据为null

```json
{
    "data":null,
    "database":"canal_test",
    "es":1633141699000,
    "id":1,
    "isDdl":true,
    "mysqlType":null,
    "old":null,
    "pkNames":null,
    "sql":"/* ApplicationName=IntelliJ IDEA 2021.2.1 */ create index idx_bas_hub_in_01
    on bas_hub_in (id, code)",
    "sqlType":null,
    "table":"bas_hub_in",
    "ts":1633144161500,
    "type":"CINDEX"
}

```

修改mysql配置 并重启mysql

```sh
[mysqld]
log-bin=mysql-bin
binlog_format=ROW
binlog_row_image=FULL
```

```json
{
    "data":[  			
        {
            "id":"1",
            "coid":"KERRYCN",
            "isusing":"1",
            "code":"TH-P-SZF",
            "name":"泰国空运普货深圳出货",
            "hub_type":"TH",
            "label_format":null,
            "a4_format":null,
            "invoice_format":null,
            "label_copies":null,
            "a4_copies":null,
            "invoice_copies":null,
            "edi_id":null,
            "export_interface_name":null,
            "post_client":null,
            "post_name":null,
            "declare_info":null,
            "rm_country_code":null,
            "rm_country_name":null,
            "rm_name":null,
            "rm_tel":null,
            "rm_addr":null,
            "rm_state":null,
            "rm_city":null,
            "rm_zip":null,
            "create_user_name":null,
            "create_datetime":null,
            "modify_user_name":"canalTest3",
            "modify_datetime":"2021-10-02 16:38:10",
            "isdel":"0",
            "hub_in_id":"118",
            "hubin_transfer_order_get_way":null,
            "iszdph":"2",
            "stano":null
        }
    ],
    "database":"canal_test",
    "es":1633163890000,
    "id":7,
    "isDdl":false,
    "mysqlType":{
        "id":"int",
        "coid":"varchar(50)",
        "isusing":"tinyint",
        "code":"varchar(20)",
        "name":"varchar(50)",
        "hub_type":"varchar(10)",
        "label_format":"int",
        "a4_format":"int",
        "invoice_format":"int",
        "label_copies":"int",
        "a4_copies":"int",
        "invoice_copies":"int",
        "edi_id":"int",
        "export_interface_name":"varchar(20)",
        "post_client":"varchar(150)",
        "post_name":"varchar(150)",
        "declare_info":"varchar(150)",
        "rm_country_code":"varchar(20)",
        "rm_country_name":"varchar(60)",
        "rm_name":"varchar(100)",
        "rm_tel":"varchar(40)",
        "rm_addr":"varchar(200)",
        "rm_state":"varchar(80)",
        "rm_city":"varchar(80)",
        "rm_zip":"varchar(10)",
        "create_user_name":"varchar(30)",
        "create_datetime":"timestamp",
        "modify_user_name":"varchar(30)",
        "modify_datetime":"timestamp",
        "isdel":"tinyint",
        "hub_in_id":"int",
        "hubin_transfer_order_get_way":"varchar(50)",
        "iszdph":"int",
        "stano":"varchar(20)"
    },
    "old":[
        {
            "modify_user_name":"canalTest2",
            "modify_datetime":"2021-10-02 16:37:43"
        }
    ],
    "pkNames":[
        "id"
    ],
    "sql":"",
    "sqlType":{
        "id":4,
        "coid":12,
        "isusing":-6,
        "code":12,
        "name":12,
        "hub_type":12,
        "label_format":4,
        "a4_format":4,
        "invoice_format":4,
        "label_copies":4,
        "a4_copies":4,
        "invoice_copies":4,
        "edi_id":4,
        "export_interface_name":12,
        "post_client":12,
        "post_name":12,
        "declare_info":12,
        "rm_country_code":12,
        "rm_country_name":12,
        "rm_name":12,
        "rm_tel":12,
        "rm_addr":12,
        "rm_state":12,
        "rm_city":12,
        "rm_zip":12,
        "create_user_name":12,
        "create_datetime":93,
        "modify_user_name":12,
        "modify_datetime":93,
        "isdel":-6,
        "hub_in_id":4,
        "hubin_transfer_order_get_way":12,
        "iszdph":4,
        "stano":12
    },
    "table":"bas_hub_in",
    "ts":1633163891017,
    "type":"UPDATE"
}
```

```java
    //数据
    private List<TbCommodityInfo> data;
    //数据库名称
    private String database;
    private long es;
    //递增，从1开始
    private int id;
    //是否是DDL语句
    private boolean isDdl;
    //表结构的字段类型
    private MysqlType mysqlType;
    //UPDATE语句，旧数据
    private String old;
    //主键名称
    private List<String> pkNames;
    //sql语句
    private String sql;
    private SqlType sqlType;
    //表名
    private String table;
    private long ts;
    //(新增)INSERT、(更新)UPDATE、(删除)DELETE、(删除表)ERASE等等
    private String type;
```

