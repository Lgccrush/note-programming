### Window安装zookeeper

[下载地址](https://zookeeper.apache.org/releases.html)

1. 下载source 解压

2. 修改conf/zoo_sample.cfg 为zoo.cfg 不修改启动会报找不到cfg配置文件

3. 在bin/zkServer.cmd 末尾添加pause 可以看启动报错信息

4. bin/zkServer.cmd 启动

   