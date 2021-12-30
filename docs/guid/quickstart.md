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

添加`application.yml`

```yml
auth-server: http://10.122.0.56:20880 # 认证服务器地址
spring:
  application:
    name: product-service
  jackson:
    time-zone: GMT+8
    date-format: yyyy-MM-dd HH:mm:ss
  cloud:
    discovery:
      enabled: true
    consul:
      host: 10.122.0.56 #consul服务器的主机地址
      port: 8500 #consul服务器的端口有
      discovery:
        register: true
        service-name: ${spring.application.name}
        #服务的请求端口
        port: ${server.port}
        #指定开启ip地址注册
        prefer-ip-address: true
        health-check-timeout: 10s
        ip-address: 10.122.3.176
      config:
        enabled: false
        fail-fast: false
        default-context: ${spring.application.name}
        format: YAML
        # config/shop-service/data
    stream:
      bindings:
        consumer-in-0:
          destination: consumer
          group: hello
      function:
        definition:
          consumer;
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://10.122.0.56:3306/TEST?useUnicode=true&characterEncoding=utf8
    username: root
    password: Nuzar123456_
    hikari:
      idle-timeout: 60000
      validation-timeout: 60000
      max-lifetime: 360000
      maximum-pool-size: 10
  redis:
    database: 1
    host: 10.122.0.56
    port: 6379
    connect-timeout: 1
    password: Nuzar123456_
  redis-lock:
    enable: true
  security:
    oauth2:
      resourceserver:
        opaquetoken:
          client-secret: bossnine
          client-id: trusted-tops
          introspection-uri: ${auth-server}/saas/token/info
  rabbitmq:
    host: 10.122.0.56
    username: admin
    password: Nuzar123456_
server:
  port: 8193

nuzar:
  cloud:
    authorization:
      permit-all: /actuator/**
    job:
      enabled: true
      admin-addresses: http://10.122.0.56:8491/xxl-job-admin
      access-token: Nuzar123456_
      executor:
        appname: ${spring.application.name}
    audit:
      enabled: false
      callable: audit_proc
    security:
      saas:
        enabled: true
    mybatis:
      tenant:
        enabled: true
        include-tables:
          - apple
        tenant-column: tenant_id
    echo:
      enabled: true
      redis:
        expire: 300

logging:
  level:
    root: info
    com:
      nuzar: DEBUG
```

## 开始写文档

初始化成功后，可以看到 `./docs` 目录下创建的几个文件

- `index.html` 入口文件
- `README.md` 会做为主页内容渲染
- `.nojekyll` 用于阻止 GitHub Pages 忽略掉下划线开头的文件

直接编辑 `docs/README.md` 就能更新文档内容，当然也可以[添加更多页面](zh-cn/more-pages.md)。

## 本地预览

通过运行 `docsify serve` 启动一个本地服务器，可以方便地实时预览效果。默认访问地址 http://localhost:3000 。

```bash
docsify serve docs
```

?> 更多命令行工具用法，参考 [docsify-cli 文档](https://github.com/docsifyjs/docsify-cli)。

## 手动初始化

如果不喜欢 npm 或者觉得安装工具太麻烦，我们可以直接手动创建一个 `index.html` 文件。

*index.html*

```html
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <meta charset="UTF-8">
  <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/vue.css">
</head>
<body>
  <div id="app"></div>
  <script>
    window.$docsify = {
      //...
    }
  </script>
  <script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
</body>
</html>
```

如果你的系统里安装了 Python 的话，也可以很容易地启动一个静态服务器去预览你的网站。

```python2
cd docs && python -m SimpleHTTPServer 3000
```

```python3
cd docs && python -m http.server 3000
```

## Loading 提示

初始化时会显示 `Loading...` 内容，你可以自定义提示信息。


```html
  <!-- index.html -->

  <div id="app">加载中</div>
```

如果更改了 `el` 的配置，需要将该元素加上 `data-app` 属性。

```html
  <!-- index.html -->
  <div data-app id="main">加载中</div>

  <script>
    window.$docsify = {
      el: '#main'
    }
  </script>
```

对比 [el 设置](zh-cn/configuration.md#el)。
