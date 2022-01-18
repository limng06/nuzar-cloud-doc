# 断路器

## 配置

[circuitbreaker spring-boot configuration](https://resilience4j.readme.io/docs/getting-started-3#configuration)

## 执行顺序

Retry ( CircuitBreaker ( RateLimiter ( TimeLimiter ( Bulkhead ( Function ) ) ) ) )

## 默认行为

默认采用线程池的隔离方式，默认会输出断路器关闭、异常等日志，默认超时时间为5S，暂未添加可配置参数，原则上超出5S即视该接口为异常。

断路器打开的条件为一定时间内超过一般的请求超时或者请求失败

默认metrics使用时间窗口，长度为60S，最小请求数为100

## fallback

请使用FallbackFacotory的方式，根据异常类型进行具体的处理

## 其它

上述方式利用的是spring-cloud-circuitbreaker的抽象层。其它状况下可以直接使用BulkHead、CircuitBreaker、TimeLimitor等特性。
