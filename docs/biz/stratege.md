# 策略模式

适当使用策略模式可以有效减小代码规模、优化目录结构，使代码功能一目了然。

## 1.提炼抽象接口

使用工厂模式的方式获取符合策略的实现类

```java
public interface Truck {    
  void run();
}
```

## 2.定义实现类

```java 1
@Component
@BizBean(key = "big", clazz = Truck.class)
public class BigTruck implements Truck {
    @Override
    public void run() {
        System.out.println("BigTruck Running!!!!!!!!");
    }
}
```

```java 2
@Component
@BizBean(key = "little", clazz = Truck.class)
public class LittleTruck implements Truck {
    @Override
    public void run() {
        System.out.println("LittleTruck Running!!!!!!!!");
    }
}
```

## 3.通过BizFactory调用

```java
@RestController
public class MainController {
    @Resource
    BizFactory<Truck> bizFactory;

    @RequestMapping(value = "/truck", method = RequestMethod.GET)
    public Object truck() {
        bizFactory.get("big").run();
        bizFactory.get("little").run();
        return "hello truck!";
    }
}
```
