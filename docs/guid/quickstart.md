# 快速开始

安装 [maven3.x](https://maven.apache.org/download.cgi) 工具，并配置私服地址。
安装[git](https://www.git-scm.com/download/win)工具。

```xml
<servers>
  <server>
      <id>public</id>
      <username>developer</username>
      <password>developer</password>
  </server>
</servers>
<mirrors>
  <mirror>
      <id>public</id>
      <mirrorOf>*</mirrorOf>
      <name>Public Repositories</name>
      <url>http://10.122.0.56:8081/repository/maven-public/</url>
  </mirror>
<mirrors>
```

## 包资源清单

nuzar-core
nuzar-business

nuzar-web-starter(回显)
nuzar-redis-starter(缓存、锁)
nuzar-echo-starter(回显)
nuzar-apm-starter(监控，未完)
nuzar-consul-starter(consul服务发现、配置
nuzar-nacos-starter(nacos服务发现、配置)
nuzar-xxl-starter(xxl调度)
nuzar-business-starter(设计模式)
nuzar-stream-starter(未完)

## 初始化项目

创建一个新maven项目，使用pom管理。

在`pom.xml`引入核心包

```xml
<properties>
    <nuzar.cloud.version>2.6.1-SNAPSHOT</nuzar.cloud.version>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
</properties>

<dependencies>
    <dependency>
        <groupId>com.nuzar</groupId>
        <artifactId>nuzar-web-starter</artifactId>
        <version>${nuzar.cloud.version}</version>
    </dependency>
</dependencies>
<build>
    <finalName>${project.artifactId}</finalName>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.5.7</version>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <finalName>${project.artifactId}</finalName>
                <mainClass>com.nuzar.service.XxxApplication</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
```

添加`application.yml`，以下为最小配置，创建一个最简单的程序

```yml
spring:
  application:
    name: hello-service
  jackson:
    time-zone: GMT+8
    date-format: yyyy-MM-dd HH:mm:ss
server:
  port: 8080

logging:
  level:
    root: info
    com:
      nuzar: DEBUG
nuzar:
  cloud:
    security:
      mode: none
```

添加`MainController.java`

```java
@RequestMapping("/mini")
@RestController
public class MainController {

    /**
     * @param id
     * @return
     */
    @GetMapping("/{id}")
    public Student get(@PathVariable String id) {
        Student student = new Student();
        student.setId(id);
        student.setName("hello world");
        return student;
    }

    /**
     * @return
     */
    @GetMapping("/exception")
    public Student exception() {
        throw BizExceptionGenerator.generateBizException(new BizExceptionDef("ERROR-001", "呵呵 发生了 一场"));
    }

    @Data
    public static class Student implements Serializable {
        private String id;
        private String name;
    }
}
```

添加启动类`MiniApplication.java`, 因为框架包含了rabbit和datasource如果不使用需要手动排除

```java
@SpringBootApplication(exclude = {
        RabbitAutoConfiguration.class,
        DataSourceAutoConfiguration.class}
)
public class MiniApplication {
    public static void main(String[] args) {
        SpringApplication.run(MiniApplication.class, args);
    }
}
```

启动程序即可调用接口

## 实例

更多实例请参考[nuzar-cloud-example](https://gitee.com/mad_max621/nuzar-cloud-example.git)下的**0.quickstart**
