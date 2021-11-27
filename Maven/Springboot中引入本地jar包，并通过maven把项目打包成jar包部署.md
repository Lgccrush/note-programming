#### 把jar包放在指定位置 pom.xml里面定义依赖

jar包位置 在src/libs下  pom.xml定义依赖

```xml
<dependency>
  <groupId>com.jdorder</groupId>
  <artifactId>ajdorder</artifactId>
  <version>1.1.8</version>
  <scope>system</scope>
  <systemPath>${project.basedir}/src/libs/lop-open-sdk.jar</systemPath><!-- jar包路径-->
</dependency>
```

![image-20211127135812780](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/11/27/13:58:12_image-20211127135812780.png)

#### 在打包插件中引入参数

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <configuration>
        <includeSystemScope>true</includeSystemScope><!-- 引入参数 -->
      </configuration>
    </plugin>
  </plugins>
</build>
```

