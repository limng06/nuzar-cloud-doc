# 多线程

请注意多线程情景下的上下文传递

如果直接开启线程请使用`InheritableThreadLocal`

如果使用线程池请使用 `TransmittableThreadLocal`

所以需要用到ThreadLocal的地方，请尽量使用`TransmittableThreadLocal`
