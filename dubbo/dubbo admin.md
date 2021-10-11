### dubbo admin

[github地址](https://github.com/apache/dubbo-admin)

#### 本地使用

clone项目到本地

```sh
git clone https://github.com/apache/dubbo-admin.git
```

在idea中打开 

修改dubbo-admin-server的application.properties

```properties
admin.registry.address=zookeeper://127.0.0.1:2181
admin.config-center=zookeeper://127.0.0.1:2181
admin.metadata-report.address=zookeeper://127.0.0.1:2181
```

启动dubbo-admin-server服务

```
dubbo-admin-server/src/main/java/org/apache/dubbo/admin/DubboAdminApplication.java
```

启动dubbo-admin-ui

```shell
npm run dev	
```

访问localhost:8080 账号 和 都是 都是 root

如果访问报错 **权限验证失败(401)**
检查修改拦截器DefaultPreHandle代码 

```java
位置dubbo-admin-server/src/main/java/org/apache/dubbo/admin/authentication/impl/DefaultPreHandle.java
  原代码
    @Value("${admin.check.sessionTimeoutMilli:3600000}")
    private long sessionTimeoutMilli;
由于@Value没有生效 sessionTimeoutMilli值为0 影响下面的判断导致没有放行通过
所以给sessionTimeoutMilli赋值即可
  改为
   //@Value("${admin.check.sessionTimeoutMilli:3600000}")
    private long sessionTimeoutMilli = 		 Long.parseLong(System.getProperty("admin.check.sessionTimeoutMilli", "3600000")); 
```

[@Value注解失效参考](https://www.cxyzjd.com/article/u013212754/102901436)

### 服务器使用

clone项目到服务器

```sh
git clone https://github.com/apache/dubbo-admin.git
```

修改dubbo-admin-server的application.properties

在dubbo-admin下打包

```sh
mvn clean package -Dmaven.test.skip=true
```

运行

```sh
cd dubbo-admin-distribution/target; 
java -jar dubbo-admin-0.1.jar
```

默认访问地址 服务器地址:8080
如果访问报错 **权限验证失败(401)**同样检查修改DefaultPreHandle代码 

支持前后的分开部署

[dubbo文档](https://dubbo.apache.org/docs/v2.7/admin/ops/introduction/)

