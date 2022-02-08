# 配置

## 详细配置

### bootstrap.yml

使用nacos配置中心

``` yml
spring:
  application:
    name: shop-service-nacos
  jackson:
    time-zone: GMT+8
    date-format: yyyy-MM-dd HH:mm:ss
  cloud:
    nacos:
      config:
        enabled: true
        namespace: nuzar-cloud-sample
        file-extension: yaml
        prefix: ${spring.application.name}
      server-addr: 10.122.0.56:8848
      discovery:
        namespace: nuzar-cloud-sample
      username: nacos
      password: nacos
  profiles:
    active: dev
server:
  port: 8194
```

### application.yml

``` yml need
server:
  port: 8210
nuzar:
  cloud:
    security:
      mode: none
logging:
  level:
    root: info
    com:
      nuzar: DEBUG
```

nuzar-cloud 部分

``` yml nuzar_cloud part
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
    # 配置审计存储过程，程序借助此存储过程将当前运行上下文发送到数据库session上下文中，以便trigger获取
    security:
      saas:
        enabled: true
      # saas 模式，是否通过saas服务校验token
      mode: classic
      # none|classic|PASSPORT 分别对应 跳过安全认证|使用oauth2认证|passport认证(开发中)
    mybatis:
      tenant:
        enabled: true
        # 是否启动多租户模式
        include-tables:
          - apple
          - product
        tenant-column: tenant_id
        # 配置租户字段
      auto-inject-sql: true
      # 默认为true 自动注入管理字段
    echo:
      enabled: true
      # 是否启动回显功能 默认为false 需要手动开启
      redis:
        expire: 1800
      # 配置redis缓存过期时间
      multi-env: true
      # 是否启动多租户环境模式，适用于多租户缓存隔离
      local:
        expire: 300
      # 缓存分两层，此处为本地缓存过期时间，使用caffeine
    redis-lock:
      enabled: true
      # 默认开启 分布式锁 默认为true
      wait-time: 500
      # 默认等待时间 500MS
      lease-time: 30000
      # 默认超时时间 30000MS
```

使用feign时需要添加以下配置，变更feign的默认实现为okhttp，修改默认connect timeout时间

``` yml feign
feign:
  okhttp:
    enabled: true
  client:
    config:
      default:
        connectTimeout: 500
```
