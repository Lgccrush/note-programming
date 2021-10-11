#### Docker

[Docker Linux 文档](https://docs.docker.com/engine/install/centos/) 

[Docker 笔记](https://blog.csdn.net/qq_21197507/article/details/115071715) 
#### Linux查看属性

```
uname -r
cat /etc/os-release 
free -m  查看虚拟机内存
```

#### 安装docker

1 删除

```
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

2安装 

```
需要的安装包 yum install -y yum-utils
```

```
配置阿里云镜像  yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

更新yum      yum makecache fast
```

```
安装docker   sudo yum install docker-ce docker-ce-cli containerd.io
```

3启动 

```
systemctl start docker
```

4查看

```
docker version
docker -v
docker images
docker ps
docker stats -a 容器运行状态

查看docker的运行状态
systemctl status docker
```

5其他

```
重启docker systemctl restart docker
开机自启    systemctl enable docker
删除容器 	docker rm 容器名
重启容器	docker restart 容器名
交互模式 	docker exec -it 容器名(标识) /bin/bash
开机自启容器 docker update 容器名 --restart=always
查看容器日志 docker logs 容器名(标识)
```

#### docker安装MySQL

```
docker pull mysql:5.7

配置端口与挂载
docker run -p 3306:3306 --name mysql \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7

修改mysql的配置文件
vi my.cnf

mysql的配置文件如下：

[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
init_connect='SET collation_connection = utf8_unicode_ci' 
init_connect='SET NAMES utf8' 
character-set-server=utf8 
collation-server=utf8_unicode_ci 
skip-character-set-client-handshake
skip-name-resolve
```

#### docker安装Redis

```
docker pull redis

先创建文件
mkdir -p /mydata/redis/conf
touch /mydata/redis/conf/redis.conf

配置端口与挂载
docker run -p 6379:6379 --name redis -v /mydata/redis/data:/data \
-v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
-d redis redis-server /etc/redis/redis.conf 


客户端 docker exec -it redis redis-cli
持久化 vi redis.conf  -->   appendonly yes

```

#### docker安装elasticsearch 

```javascript
安装
docker pull elasticsearch:7.4.2

用于挂载
mkdir -p /mydata/elasticsearch/config
mkdir -p /mydata/elasticsearch/data
配置可以被访问
echo "http.host: 0.0.0.0">>/mydata/elasticsearch/config/elasticsearch.yml

http.host:后面要用空格否则启动报错
(ParsingException[Failed to parse object: expecting token of type [START_OBJECT] but found [VALUE_STRING]])

 
运行 
docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms64m -Xmx512m" \
-docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms64m -Xmx512m" \
-v /mydata/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /mydata/elasticsearch/data:/usr/share/elasticsearch/data \
-v /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-d elasticsearch:7.4.2

启动报错
Caused by: java.nio.file.AccessDeniedException: /usr/share/elasticsearch/data/nodes
改elasticsearch访问权限
chmod -R 777 /mydata/elasticsearch/


访问用端口9200
```

#### docker安装kibana

```
docker pull kibana:7.4.2

117.50.106.252 为自己的虚拟机地址
docker run --name kibana -e ELASTICSEARCH_HOSTS=http://117.50.106.252:9200 -p 5601:5601 \
-d kibana:7.4.2

启动报错Kibana server is not ready yet
将配置文件kibana.yml中的elasticsearch.url改为正确的链接，默认为: http://elasticsearch:9200
改为http://自己的IP地址:9200
```

#### docker安装RabbitMQ

```
docker run -d --name rabbitmq -p 5671:5671 -p 5672:5672 -p 4369:4369 -p 25672:25672 -p 15671:15671 -p 15672:15672 rabbitmq:management
```

#### docker安装Nginx

```
下载并运行
docker run -p 80:80 --name nginx -d nginx:1.10

在mydata下创建nginx文件夹
mkdir nginx

复制配置到nginx
docker container cp nginx:/etc/nginx .

停止并删除 nginx
docker stop nginx 
docker rm nginx

修改复制到nginx中的配置的文件夹名 把nginx改为conf
mv nginx conf

docker run -p 80:80 --name nginx \
-v /mydata/nginx/html:/usr/share/nginx/html \
-v /mydata/nginx/logs:/var/log/nginx \
-v /mydata/nginx/conf:/etc/nginx \
-d nginx:1.10

测试是否成功 在nginx/html下 创建index.html 访问80端口

在nginx中存储自定义分词 在html文件中创建es文件夹并创建fenci.txt存自定义分词
```

#### docker安装ik分词器

```
找到与elasticsearch相同版本的ik 
	1在plugins中 通过wget 可以远程下载 
		安装wget用 yum install wget
		安装unzip用 yum install unzip
	2上传ik压缩包到plugins 使用unzip
配置nginx中存储自定义分词
	在ik的conf 的IKAnalyzer.cfg.xml配置 nginx存储的分词路径
    
	<!--用户可以在这里配置远程扩展字典 --> 打开注释配置 并重启es
    <!-- <entry key="remote_ext_dict">words_location</entry> -->
	
```

#### node 配置

```
npm config set registry http://registry.npm.taobao.org/ 配置镜像
node -v 查看
```

 