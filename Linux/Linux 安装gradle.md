#### 前置条件 需要有java环境

![image-20211127134049460](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/11/27/13:40:49_image-20211127134049460.png)

#### 下载安装gradle

```shell
以5.3.1版本为例
$ sudo mkdir /opt/gradle
$ cd /opt/gradle
$ sudo wget https://services.gradle.org/distributions/gradle-5.3.1-all.zip
$ sudo unzip -d /opt/gradle gradle-5.3.1-all.zip
```

![image-20211127134735419](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/11/27/13:47:35_image-20211127134735419.png)

#### 配置环境变量

```shell
vim /etc/profile
```

![image-20211127134425412](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/11/27/13:44:25_image-20211127134425412.png)

```sh
source /etc/profile #刷新环境变量
gradle -v #查看gradle是否安装成功
```

![image-20211127134911753](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/11/27/13:49:11_image-20211127134911753.png)