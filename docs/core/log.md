# 日志

## nuzar-apm-starter

代码中的日志请统一使用SLF4J框架，默认使用spring-boot的logback日志实现

使用 `@LogRecord` 记录接口日志

## 扩展

使用SPI方式扩展`LogRecordProcessor`接口，添加自己的日志存储模块