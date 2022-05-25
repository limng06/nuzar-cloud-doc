# 日志

## nuzar-logging-starter

代码中的日志请统一使用SLF4J框架，默认使用spring-boot的logback日志实现
如果需要可使用logback的appender组件配合elk使用

## 接口日志扩展

使用 `@LogRecord` 记录接口日志，默认提供`DefaultLogRecordProcessor`，默认会将接口日志打印到控制台

支持使用SPI方式扩展`LogRecordProcessor`接口，添加自己的日志存储模块，例如可以将接口日志保存到数据库等，如果需要使用spring注入，请将其以@Bean的方式注入
