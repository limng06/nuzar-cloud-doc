# 快速开始

安装 `maven` 工具，并配置私服地址。

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

添加`application.yml`，以下为最小配置

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
