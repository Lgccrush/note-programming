### Docker 安装 zookeeper 和 dubbo

1. 安装 zookeeper

   ```bash
   docker run --name zookeeper -p 2181:2181 -d zookeeper
   ```

2. 获取zookeeper容器地址  看IPAddress

   ```bash
   docker inspect zookeeper 
   ```

   

1. 安装dubbo

   ```bash
   docker pull chenchuxin/dubbo-admin
   ```

2. 启动

   ```bash
    docker run -it -d --name dubbo-admin \
   > -v /mydata/dubbo-admin:/data \   #这里是挂载目录
   > -p 8181:8080 \ #这里是端口号映射
   > -e dubbo.registry.address=zookeeper://172.18.0.5:2181 \ #这里是zookeeper的IPAddress
   > -e dubbo.admin.root.password=guest \ #控制台root用户访问密码
   > -e dubbo.admin.guest.password=guest \ #控制台guest用户访问密码
   > chenchuxin/dubbo-admin 
   
   ```

   