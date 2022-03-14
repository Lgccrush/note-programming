### Window安装Tomcat

[Tomcat8下载地址](https://tomcat.apache.org/download-80.cgi)

1. 下载zip 解压
2. 配置环境变量
3. bin/startup.bat 启动
4. 启动中文乱码 修改conf/logging.properties 的java.util.logging.ConsoleHandler.encoding=GBK
5. 启动报错org.apache.catalina.LifecycleException: 协议处理程序初始化失败 可能是端口占用 修改conf/server.xml里Connector port="8080"