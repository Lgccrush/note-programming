[Github 地址](https://github.com/portainer/portainer)

[在线体验](http://demo.portainer.io/)(用户名：admin ，密码：tryportainer)

![image-20211011180819085](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/11/18:08:19_image-20211011180819085.png)

1.首先下载 Portainer 的 Docker 镜像

```sh
docker pull portainer/portainer-ce
```

2.再使用如下命令运行 Portainer 容器

```sh
docker run -p 9000:9000 -p 8000:8000 --name portainer \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /mydata/portainer/data:/data \
-d portainer/portainer
```

第一次登录的时候需要创建管理员账号，访问地址：服务器地址:9000/![image-20211011180956986](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/11/18:09:57_image-20211011180956986.png)

![image-20211011181008882](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/11/18:10:08_image-20211011181008882.png)