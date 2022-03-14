### Window安装dubbo

[下载地址](https://github.com/alibaba/dubbo/tree/2.5.x)

#### 控制台(admin)的搭建

1. 解压zip
2. 在当前目录下使用命令mvn clean install -Dmaven.test.skip=true构建dubbo源码 war包
3. 构建完成之后,把dubbo-admin下target文件夹中的war包,将其拷贝到本地的tomcat的webapps下
4. 然后启动tomcat,在浏览器中输入地,我拷贝到tomcat–>webapps下的dubbo-admin的war包我改名为了dubbo-admin.war,如下图所示,我的tomcat端口号是8888,在浏览其中输入地址http://localhost:8888/dubbo-admin。
   **注意在启动服务前需要先启动zookeeper**

---

#### **监控中心(Monitor)的搭建**

1. 修改\dubbo-2.5.x\dubbo-simple\dubbo-monitor-simple\target\dubbo-monitor-simple-2.5.10-assembly\dubbo-monitor-simple-2.5.10\conf\dubbo.properties

   ```properties
   dubbo.container=log4j,spring,registry,jetty
   dubbo.application.name=simple-monitor
   dubbo.application.owner=
   #dubbo.registry.address=multicast://224.5.6.7:1234
   dubbo.registry.address=zookeeper://127.0.0.1:2181
   #dubbo.registry.address=redis://127.0.0.1:6379
   #dubbo.registry.address=dubbo://127.0.0.1:9090
   dubbo.protocol.port=7070
   dubbo.jetty.port=8099
   dubbo.jetty.directory=${user.home}/monitor
   dubbo.charts.directory=${dubbo.jetty.directory}/charts
   dubbo.statistics.directory=${user.home}/monitor/statistics
   dubbo.log4j.file=logs/dubbo-monitor-simple.log
   dubbo.log4j.level=WARN
   ```

2. 进入D:\lgc\dev_tools\dubbo-2.5.x\dubbo-simple\dubbo-monitor-simple\target\dubbo-monitor-simple-2.5.10-assembly\dubbo-monitor-simple-2.5.10\bin

   start.sh执行 通过8099访问