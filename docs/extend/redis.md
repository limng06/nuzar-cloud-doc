# REDIS 分布式缓存/分布式锁

## 依赖

```xml
<dependency>
    <groupId>com.nuzar</groupId>
    <artifactId>nuzar-redis-starter</artifactId>
    <version>${nuzar.cloud.version}</version>
</dependency>
```

## 测试环境

使用单点模式部署

## 生产环境

使用哨兵模式部署

## 缓存

请注意几个RedisTemplate的区别，默认的RedisTemplate的key已经修改为String格式，value为二进制，如果需要存储String对象请使用StringRedisTemplate

### RedisTemplate

key 为`String` value为二进制

### StringRedisTemplate

key 为`String` value为`String`，空间更小，效率更好

### JsonRedisTemplate

key 为`String` value为JSON对象，提供更好的可读性
