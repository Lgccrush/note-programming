### TMS  kafka

#### kafka predictor

发送消息前存日志 消息状态为**待发送**

消息成功回调修改日志的消息状态为**已发送**

消息失败回调是只打了日志 有job每5秒获取待发送消息并发送

#### kafka customer

使用 **@KafkaListener**

收到消息修改日志的状态为**已消费**

消费异常修改日志的状态为**消费异常** 并 发送消息给对应的result topic

消费成功修改日志的状态为**已消费** 并 发送消息给对应的result topic

#### dubbo

启动类加**@EnableDubbo**

TMS单独创建项目tms-saas-api 包含实体类和接口

在web模块下实现api的接口 实现类上标注dubbo的**@Service**注解 指定端口和版本号

OPS/POS使用dubbo 在gradle引入tms-saas-api gradle 注入调用的接口标注dubbo的**@Reference** 指定版本号

#### Canal+ES+kafka

Canal监听manifest相关表 做为生产者通过不同的topic发送到kafka

TMS消费topic消息 存储到ES

![image-20211011142819025](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/11/14:28:19_image-20211011142819025.png)