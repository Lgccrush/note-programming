#### 引入相关jar包

```xml
  				<!--mysql-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.23</version>
        </dependency>
        <!--mybatis-plus-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.1</version>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
            <version>2.3.28</version>
            <scope>compile</scope>
        </dependency>
```

#### 配置文件

```yml
spring:
  datasource: ##数据源配置
    username: root
    password: root
    url: jdbc:mysql://121.4.225.208:3306/wxpay?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai&rewriteBatchedStatements=true
    driver-class-name: com.mysql.cj.jdbc.Driver
mybatis-plus: ##配置xml位置
  mapper-locations: classpath:/mapper/**/*.xml
  global-config:
    db-config: ##配置id自增
      id-type: auto

```

```properties
rewriteBatchedStatements=true #开启批量
 mapper-locations: classpath:/mapper/**/*.xml #xml在resource下面
```

==**1启动报错No qualifying bean of type ‘XXX‘ available**== 

==**启动类未添加注解 @MapperScan("com.example.springbootdockertest.mapper")**== 

[参考博客](https://blog.csdn.net/weixin_44338444/article/details/122820284)

==**2启动报错org.apache.ibatis.binding.BindingException: Invalid bound statement (not found)**==

==**xml位置不对==**[参考博客](https://blog.csdn.net/sundacheng1989/article/details/81630370)

![image-20220404113253037](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/04/04/11:32:53_image-20220404113253037.png)



#### 代码生成

```java
import com.baomidou.mybatisplus.generator.FastAutoGenerator;
import com.baomidou.mybatisplus.generator.config.OutputFile;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

import java.util.Collections;

/**
 * MybatisPlus代码生成
 *
 * @Author liguangcheng
 * @Date 2022/4/3 1:57 下午
 * @Vision 1.0
 **/
public class GenerateCode {
    public static void main(String[] args) {
        FastAutoGenerator.create("jdbc:mysql://121.4.225.208:3306/wxpay?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC&allowMultiQueries=true", "root", "root")
                .globalConfig(builder -> {
                    builder.author("crush") // 设置作者
                            // .enableSwagger() // 开启 swagger 模式
                            .commentDate("yyyy-MM-dd") // 设置注释日期的格式
                            .fileOverride() // 覆盖已生成文件
                            .outputDir("/Users/liguangcheng/IdeaProjects/spring-boot-docker-test/src/main/java"); // 指定输出目录
                })
                .packageConfig(builder -> {
                    builder.parent("com.example.springbootdockertest") // 设置父包名
// .moduleName("") // 设置父包模块名
                            .entity("entity.DO") // 设置实体类生成位置，DO不能小写，是关键字
                            .controller("controller.paymentInfo") // 设置实体类生成位置，DO不能小写，是关键字
                            .pathInfo(Collections.singletonMap(OutputFile.xml, "/Users/liguangcheng/IdeaProjects/spring-boot-docker-test/src/main/resources/mapper"));// 设置mapperXml生成路径
                })
                .strategyConfig(builder -> {
                    builder.addInclude("t_payment_info") // 设置需要生成的表名
                            .addTablePrefix("t_") // 设置过滤表前缀,在生成实体类时会将过滤的表前缀去掉
                            .entityBuilder()// 进入实体类生成配置
                            .enableLombok()// 开启lombok，默认false
                            .enableRemoveIsPrefix()// 设置将表中字段的is前缀删除
                            .enableTableFieldAnnotation()// 开启生成实体时生成字段注解
                            .formatFileName("%sDo")// 设置生成实体类的文件名后缀
                            .build()
                            .serviceBuilder()// 进入服务层生成配置
                            .formatServiceFileName("%sService")// 设置生成服务层的文件名后缀，不设置的话服务层首字母会添加一个I
                            .build();
                })
                .templateEngine(new FreemarkerTemplateEngine()) // 使用Freemarker引擎模板，默认的是Velocity引擎模板
                .execute();

    }
}

```

![image-20220404114605838](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/04/04/11:46:05_image-20220404114605838.png)