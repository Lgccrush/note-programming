### spring boot dubbo

**基于spring 2.5.3**

#### 生产者
pom.xml 引入dubbo依赖（引入curator的jar
没有启动会报错java.lang.NoClassDefFoundError: org/apache/curator/framework/CuratorFrameworkFactory）

```xml
<dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>2.7.8</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>2.8.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>2.8.0</version>
</dependency>
```

application.properties

```properties
## Dubbo配置
#注册到注册中心的名称
dubbo.application.name=provider-test
#dubbo注册中心地址 zookeeper地址
dubbo.registry.address=zookeeper://127.0.0.1:2181
#采用协议方式和端口号  常用协议：dubbo,http,webService等
dubbo.protocol.name=dubbo
dubbo.protocol.port=20880
#实现类扫包范围(可以省略，dubbo会自动扫 带了@Service的类)
dubbo.scan.base-packages=com.example.springbootdockertest.servece
dubbo.provider.group=test
dubbo.provider.version=1.0
```



接口

```java
public interface DubboServiceTest {
    String dubboTest();
}
```

实现类

```java
//dubbo 2.7.7开始 注解@DubboService 之前是@Service
@DubboService(interfaceClass = DubboServiceTest.class, version = "1.0.0")
public class DubboServiceTestImpl implements DubboServiceTest {
    @Override
    public String dubboTest() {
        return "hello dubbo";
    }
}
```

启动类

```java
@SpringBootApplication
@EnableDubbo //开启dubbo
public class SpringBootDockerTestApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootDockerTestApplication.class, args);
    }

}
```

#### 消费者

pom.xml 引入dubbo依赖

```xml
<dependency>
  <groupId>org.apache.dubbo</groupId>
  <artifactId>dubbo-spring-boot-starter</artifactId>
  <version>2.7.8</version>
</dependency>
<dependency>
  <groupId>org.apache.curator</groupId>
  <artifactId>curator-framework</artifactId>
  <version>2.8.0</version>
</dependency>
<dependency>
  <groupId>org.apache.curator</groupId>
  <artifactId>curator-recipes</artifactId>
  <version>2.8.0</version>
</dependency>
```

application.properties

```properties
## Dubbo 配置
#服务名
dubbo.application.name=customer-test
#注册地址
dubbo.registry.address=zookeeper://127.0.0.1:2181
#采用协议方式和端口号  常用协议：dubbo,http,webService等
dubbo.protocol.name=dubbo
dubbo.protocol.port=20881
dubbo.consumer.group=test
dubbo.consumer.version=1.0
```



接口

```java
public interface DubboServiceTest {
    String dubboTest();
}
```

测试Controller

```java
@RestController
public class DubboCustomerTest {
  	//dubbo 2.7.7开始 注解@DubboReference 之前是@Reference 
    @DubboReference(version = "1.0.0")
    private DubboServiceTest dubboServiceTest;

    @GetMapping("dubboTest")
    public String dubboTest(){
        String dubboTest = dubboServiceTest.dubboTest();
        System.out.println(dubboTest);
        return dubboTest;
    }
}
```

启动类

```java
@SpringBootApplication
@EnableDubbo
public class DubboCustomerApplication {

    public static void main(String[] args) {
        SpringApplication.run(DubboCustomerApplication.class, args);
    }

}
```

