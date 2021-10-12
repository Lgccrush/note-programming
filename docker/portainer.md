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

### 使用

登录成功后，可以发现有一个本地的 Docker 环境；

![image-20211012093743572](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:37:43_image-20211012093743572.png)

打开 Dashboard 菜单可以看到 Docker 环境的概览信息，比如运行了几个容器，有多少个镜像等；![image-20211012093805003](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:38:05_image-20211012093805003.png)

打开 App Templates 菜单可以看到很多创建容器的模板，通过模板设置下即可轻松创建容器，支持的应用还是挺多的；![image-20211012093908404](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:39:08_image-20211012093908404.png)

打开 Containers 菜单，可以看到当前创建的容器，我们可以对容器进行运行、暂停、删除等操作；![image-20211012095114348](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:51:14_image-20211012095114348.png)

选择一个容器，点击 Logs 按钮，可以直接查看容器运行日志，可以和 docker logs 命令说再见了；![image-20211012095136869](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:51:36_image-20211012095136869.png)

点击 Inspect 按钮，可以查看容器信息，比如看看容器运行的 IP 地址；![image-20211012095225442](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:52:25_image-20211012095225442.png)

点击 Stats 按钮，可以查看容器的内存、CPU 及网络的使用情况，性能分析不愁了；![image-20211012095247987](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:52:48_image-20211012095247987.png)

点击 Console 按钮，可以进入到容器中去执行命令，比如我们可以进入到 MySQL 容器中去执行登录命令；![image-20211012095311876](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:53:12_image-20211012095311876.png)

打开 Images 菜单，我们可以查看所有的本地镜像，对镜像进行管理；

![image-20211012095332055](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:53:32_image-20211012095332055.png)

打开 Networks 菜单，可以查看 Docker 环境中的网络情况；![image-20211012095349280](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:53:49_image-20211012095349280.png)

打开 Users 菜单，我们可以创建 Portainer 的用户，并给他们赋予相应的角色；![image-20211012095412256](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:54:12_image-20211012095412256.png)

打开 Registries 菜单，我们可以配置自己的镜像仓库，这样在拉取镜像的时候，就可以选择从自己的镜像仓库拉取了。![image-20211012095431800](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/12/09:54:31_image-20211012095431800.png)

## 总结

Portainer 作为一款轻量级 Docker 图形化管理工具，功能强大且实用，要是有个私有镜像仓库管理功能就更好了，这样我们就不用安装重量级的镜像仓库 Harbor 了。