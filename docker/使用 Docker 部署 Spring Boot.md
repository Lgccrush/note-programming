使用 Docker 部署 Spring Boot

首先构建一个简单的 Spring Boot 项目，然后给项目添加 Docker 支持，最后对项目进行部署。

一个简单 Spring Boot 项目
在 pom.xml 中 ，使用 Spring Boot 2.0 相关依赖

```xml
		<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.3</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
添加 web 和测试依赖
<dependencies>
			<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
</dependencies>
```

创建一个 DockerController，在其中有一个index()方法，访问时返回：Hello Docker!

```java
@RestController
public class TestController {
    @GetMapping("test")
    public String get(){
        return "hello springBoot Docker";
    }
}
```


启动类

```java
@SpringBootApplication
public class SpringBootDockerTestApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootDockerTestApplication.class, args);
    }

}
```

application.properties 配置端口访问

```properties
server.port=9999
```

添加完毕后启动项目，启动成功后浏览器访问：http://localhost:9999/test，页面返回：hello springBoot Docker!，

说明 Spring Boot 项目配置正常。

Spring Boot 项目添加 Docker 支持
在 pom.xml-properties 中添加 Docker 镜像名称

```xml
<properties>
	<docker.image.prefix>springboot</docker.image.prefix>
</properties>
plugins 中添加 Docker 构建插件：

<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
		<!-- Docker maven plugin -->
		<plugin>
			<groupId>com.spotify</groupId>
			<artifactId>docker-maven-plugin</artifactId>
			<version>1.0.0</version>
			<configuration>
				<imageName>${docker.image.prefix}/${project.artifactId}</imageName>
				<dockerDirectory>src/main/docker</dockerDirectory>
				<resources>
					<resource>
						<targetPath>/</targetPath>
						<directory>${project.build.directory}</directory>
						<include>${project.build.finalName}.jar</include>
					</resource>
				</resources>
			</configuration>
		</plugin>
		<!-- Docker maven plugin -->
	</plugins>
</build>
```


在目录src/main/docker下创建 Dockerfile 文件，Dockerfile 文件用来说明如何来构建镜像。

```properties
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD spring-boot-docker-test.jar app.jar 
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```


这个 Dockerfile 文件很简单，构建 Jdk 基础环境，添加 Spring Boot Jar 到镜像中，简单解释一下:

FROM ，表示使用 Jdk8 环境 为基础镜像，如果镜像不是本地的会从 DockerHub 进行下载
VOLUME ，VOLUME 指向了一个/tmp的目录，由于 Spring Boot 使用内置的Tomcat容器，Tomcat 默认使用/tmp作为工作目录。这个命令的效果是：在宿主机的/var/lib/docker目录下创建一个临时文件并把它链接到容器中的/tmp目录
ADD ，拷贝文件并且重命名
ENTRYPOINT ，为了缩短 Tomcat 的启动时间，添加java.security.egd的系统属性指向/dev/urandom作为 ENTRYPOINT
这样 Spring Boot 项目添加 Docker 依赖就完成了。

构建打包环境
我们需要有一个 Docker 环境来打包 Spring Boot 项目，在 Windows 搭建 Docker 环境很麻烦，因此我这里以 Centos 7 为例。

安装 Docker 环境
安装

```bash
 yum install docker
```


安装完成后，使用下面的命令来启动 docker 服务，并将其设置为开机启动：

```bash
service docker start
chkconfig docker on
```

此处采用了旧式的 sysv 语法，如采用CentOS 7中支持的新式 systemd 语法，如下：

```bash
systemctl  start docker.service
systemctl  enable docker.service
使用Docker 中国加速器

vi  /etc/docker/daemon.json

#添加后：
{
    "registry-mirrors": ["https://registry.docker-cn.com"],
    "live-restore": true
}
```


重新启动docker

```bash
systemctl restart docker
```


输入docker version 返回版本信息则安装正常。

安装JDK

```bash
yum -y install java-1.8.0-openjdk*
配置环境变量 打开 vim /etc/profile 添加一下内容

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64 
export PATH=$PATH:$JAVA_HOME/bin 
修改完成之后，使其生效

source /etc/profile
输入java -version 返回版本信息则安装正常。
```

安装MAVEN

```bash
wget https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.tar.gz
```

## 解压

```bash
tar vxf apache-maven-3.5.2-bin.tar.gz
```

## 移动

```bash
mv apache-maven-3.5.2 /usr/local/maven3
```

修改环境变量， 在/etc/profile中添加以下几行

```properties
MAVEN_HOME=/usr/local/maven3
export MAVEN_HOME
export PATH=${PATH}:${MAVEN_HOME}/bin

记得执行source /etc/profile使环境变量生效。
source /etc/profile
```

输入mvn -version 返回版本信息则安装正常。

**报错**

```bash
JAVA_HOME should point to a JDK not a JRE
```

原因：jdk配置不规范 修改配置

```properties
vim /etc/profile 改配置
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64 
改为
配置环境变量 打开 vim /etc/profile 添加一下内容
export JAVA_HOME=/usr/lib/jvm/java
记得执行source /etc/profile使环境变量生效。
source /etc/profile
```

最终效果

```bash
export JAVA_HOME=/usr/lib/jvm/java
export PATH=$PATH:$JAVA_HOME/bin
MAVEN_HOME=/usr/local/maven3
export MAVEN_HOME
export PATH=${PATH}:${MAVEN_HOME}/bin
```



这样整个构建环境就配置完成了。

使用 Docker 部署 Spring Boot 项目
将项目 spring-boot-docker 拷贝服务器中，进入项目路径下进行打包测试。

```bash
#打包
mvn package
#启动
java -jar target/spring-boot-docker-1.0.jar
```

看到 Spring Boot 的启动日志后表明环境配置没有问题，接下来我们使用 DockerFile 构建镜像。

mvn package docker:build

构建失败情况

```bash
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 19.050 s
[INFO] Finished at: 2021-09-05T10:36:48+08:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:1.0.0:build (default-cli) on project spring-boot-docker-test: Exception caught: ADD failed: file not found in build context or excluded by .dockerignore: stat spring-boot-docker-1.0.jar: file does not exist -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:

```

检查dockerfil

```properties
ADD spring-boot-docker-test.jar app.jar #注意spring-boot-docker-test.jar 是项目打包后的jar名字
```

第一次构建可能有点慢，当看到以下内容的时候表明构建成功：

```bash
...
Step 1 : FROM openjdk:8-jdk-alpine
 ---> 224765a6bdbe
Step 2 : VOLUME /tmp
 ---> Using cache
 ---> b4e86cc8654e
Step 3 : ADD spring-boot-docker-1.0.jar app.jar
 ---> a20fe75963ab
Removing intermediate container 593ee5e1ea51
Step 4 : ENTRYPOINT java -Djava.security.egd=file:/dev/./urandom -jar /app.jar
 ---> Running in 85d558a10cd4
 ---> 7102f08b5e95
Removing intermediate container 85d558a10cd4
Successfully built 7102f08b5e95
[INFO] Built springboot/spring-boot-docker
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 54.346 s
[INFO] Finished at: 2018-03-13T16:20:15+08:00
[INFO] Final Memory: 42M/182M
[INFO] ------------------------------------------------------------------------
```

使用docker images命令查看构建好的镜像：

```xml
docker images
REPOSITORY                           TAG            IMAGE ID       CREATED          SIZE
springboot/spring-boot-docker-test   latest         3d642e8f0b01   41 minutes ago   127MB
```

springboot/spring-boot-docker-test 就是我们构建好的镜像，下一步就是运行该镜像

```bash
后台运行
docker run -itd -p 9999:9999  springboot/spring-boot-docker-test
启动完成之后我们使用docker ps查看正在运行的镜像：
docker ps
CONTAINER ID   IMAGE                                COMMAND                  CREATED          STATUS          PORTS                                       NAMES
c25169e9a8bc   springboot/spring-boot-docker-test   "java -Djava.securit…"   33 seconds ago   Up 31 seconds   0.0.0.0:9999->9999/tcp, :::9999->9999/tcp   vigorous_allen
可以看到我们构建的容器正在在运行，访问浏览器：http://192.168.0.x:9999/test,返回

hello springBoot Docker
```

说明使用 Docker 部署 Spring Boot 项目成功！