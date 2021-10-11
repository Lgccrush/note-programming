docker安装，启动tomcat 并访问

docker安装
环境centos7
1、检查内核版本，必须是3.10及以上

```sh
uname -r
```

2、安装docker

```sh
yum install docker
```

3、启动docker

```sh
[root@localhost ~]# systemctl start docker
```

4：查看版本号

```sh
[root@localhost ~]# docker -v
```

5、设置开机启动docker

```sh
[root@localhost ~]# systemctl enable docker
```

6、停止docker

```sh
systemctl stop docker
```

容器操作
1、搜索镜像

```sh
[root@localhost ~]# docker search tomcat
```

2、拉取镜像

```sh
[root@localhost ~]# docker pull tomcat
```

3、根据镜像启动容器

```sh
docker run --name mytomcat -d tomcat:latest
```

（–name ‘自己取的窗口名’） -d 表示后台运行 tomcat:latest 表示启动的哪个容器，最好用id (IMAGE_ID)d5eef28cf41d
这里启动了不能被主机访问，一是没有对外映射端口，二是可能没有关闭防火墙，三访问可能报404
所以：停掉

正确启动方式

```sh
docker run -d -it -p 8080:8080 --name tomcat tomcat:latest
-p 表示是把需拟机的8888端口映射到容器中的8080端口
```

注意，如果启动了docker但是没有启动docker中的服务(tomcat)，中间关闭防火墙，则要重启docker，不然启动tomcat会报错

最后访问报404的情况：

原因：
上面说的可能没有映射端口 或者没有关闭防火墙

```sh
service firewalld status #查看防火墙状态 
service firewalld stop #关闭
```

我遇到的是另一种情况
进入到tomcat容器中

```sh
docker exec -it 启动的tomcat容器id /bin/bash
ls -l
```

进入查看webapps 文件夹查看为空

```sh
rm -r webapps
```

我们要把webapps目录删掉，再把webapps.dist目录换成webapps

```sh
mv webapps.dist webapps
```

然后再去访问外网
http://x.x.x.x:8080/ (x.x.x.x 输入IP地址)