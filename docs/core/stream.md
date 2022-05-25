# 分布式事件传递

事件传递依赖spring-cloud-function和spring-cloud-stream实现，以消息发布订阅的方式通过mq传递

## nuzar-stream-starter

配置rabbit数据源 `rabbitmq`

```yml
spring:
  rabbitmq:
    host: 10.122.0.56
    username: admin
    password: Nuzar123456_
```

## 实例

请参考 nuzar-cloud-example 下的 8.message文件夹下，运行message-a-service和message-b-service查看实例运行结果

消息传递请注意如果设计数据库更新操作，调用源应当带token，之后的消息传递过程中用户信息将自动传递，可以通过使用

-SecurityUtils.getTenantId()
-SecurityUtils.getUserId()

获取用户id和当前用户的租户信息

在`pom.xml`引入核心包

```xml
<properties>
    <nuzar.cloud.version>2.6.7-SNAPSHOT</nuzar.cloud.version>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
</properties>

<dependencies>
    <dependency>
        <groupId>com.nuzar</groupId>
        <artifactId>nuzar-stream-starter</artifactId>
        <version>${nuzar.cloud.version}</version>
    </dependency>
</dependencies>
```

配置事件流

```yml
spring:
  cloud:
    stream:
      bindings:
        produceBizUpdateInfo:
          destination: processBizUpdateInfo
        processBizUpdateInfo-in-0:
          destination: processBizUpdateInfo
          group: a
        processBizUpdateInfo-out-0:
          destination: callBackBizUpdateInfo
        callBackBizUpdateInfo-in-0:
          destination: callBackBizUpdateInfo
          group: self
      rabbit:
        bindings:
          produceBizUpdateInfo:
            producer:
              auto-bind-dlq: true
          processBizUpdateInfo-in-0:
            consumer:
              auto-bind-dlq: true
              dlqMaxLength: 10000
          callBackBizUpdateInfo-in-0:
            consumer:
              auto-bind-dlq: true
              dlqMaxLength: 10000
      function:
        definition:
          processBizUpdateInfo;callBackBizUpdateInfo;
```
