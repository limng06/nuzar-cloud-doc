# 回显

## nuzar-echo-starter

回显功能用于填充和丰富接口返回数据，eg. Product默认返回brand字段，我们可以通过Echo回显功能将brand字段的值翻译为对应的中文名称，另外也
可以通过此功能完成马赛克功能，默认提供手机号、身份证、密码、地址的马赛克功能

***回显默认有超时时间500MS，超时后立即返回结果，即回显发生错误不会影响程序运行，可能会造成显示不完整**

## echo数据源

考虑到性能问题，目前echo数据都提前存放与redis中，可以通过实现PreEchoCacheSupplier并将其注入为Bean的方式，可以在程序启动时将需要缓存的数据载入redis中去

```java
@Bean
public PreEchoCacheSupplier preEchoCacheSupplier(SysUsersService usersService) {
    return () -> {
        List<EchoCacheObject> list = new ArrayList<>();
        List<SysUsers> allUser = usersService.list();
        list.addAll(allUser.stream().
                map(t ->
                        new EchoCacheObject(t.getTenantId(), Constants.Domains.USER, t.getId(), t.getName()))
                .collect(Collectors.toList()));
        list.addAll(allUser.stream().
                map(t ->
                        new EchoCacheObject(null, Constants.Domains.USER, t.getId(), t.getName()))
                .collect(Collectors.toList()));
        return list;
    };
}
```

## 配置

默认echo为关闭状态需要手动打开

echo使用两层缓存，redis和本地缓存

```yml
nuzar:
  cloud:
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
```

* `@Echo`

  标注需要回显的字段，默认提供两种模式，指定EchoService接口或者使用字典工厂DictDomain功能

* `@EchoResult`
  
  标注指定的接口以标识该接口需要做回显填充

添加 `Product.java`

```java
@Data
@Table(name = "product")
public class Product extends BaseEntity implements Serializable {
    private static final long serialVersionUID = 1L;
    /**
     * 主键ID
     */
    @TableId
    private String id;

    /**
     * 品牌
     */
    @Echo(echoService = BrandEchoService.class, ref = "brandName")
    private String brand;

    /**
     * 品牌中文名
     */
    private String brandName;

    /**
     * 马赛克
     */
    @Echo(echoService = SensitiveEchoService.Phone.class)
    private String phone;
    /**
     * 型号
     */
    @Echo(dictDomain = "brand")
    private String model;
    /**
     * 序列号
     */
    private Integer serialNo;
    /**
     * 价格
     */
    private Integer price;
}
```

添加 `BrandEchoService.java` 和它的实现类 `BrandEchoServiceImpl.java`

```java BrandEchoService
public interface BrandEchoService extends EchoService<String, String> {

}
```

```java BrandEchoServiceImpl
@Component
public class BrandEchoServiceImpl implements BrandEchoService {
    @Override
    public String echo(String s) {
        if ("nokia".equals(s)) {
            return "诺基亚";
        }
        return "杂牌";
    }
}
```

使用`AbstractCacheEchoService`，提供本地缓存功能，eg如下

```java AbstractCacheEchoService
@Component
@AllArgsConstructor
public class PubVoyageEchoServiceImpl extends AbstractCacheEchoService<String, String> implements PubVoyageEchoService {
    private final AcceptanceApi acceptanceApi;

    @Override
    public String echo(String s) {
        List<CommonDict> list = getFromCache("voyage", () -> acceptanceApi.queryAllPort());
        if (list == null) {
            return null;
        }
        Optional<CommonDict> first = list.stream().filter(t -> t.getId().equals(s)).findFirst();
        if (first.isPresent()) {
            return first.get().getName();
        }
        return null;
    }
}
```

添加 `ProductController.java`

```java
@RestController
@RequestMapping("/product")
public class ProductController {

    @EchoResult
    @RequestMapping(
        value = {""},
        method = {RequestMethod.GET}
    )
    public List<T> query(@QueryBody ProductQuery query) {
        return this.getService().list(query.getQueryWrapper(this.getService().getEntityClass()));
    }

    /**
     * 快速检索类
     */
    public static class ProductQuery extends QueryModel {
        @QueryParam(filterType = QueryParam.FilterType.LIKE)
        private String brand;
    }
}
```

## 扩展

使用自定义 `DictFactory` 来实现自己的字典工厂，使得字典类型的翻译更加简单

```java
@Echo(dictDomain = "brand")
private String model;
```

```java
@Component
public class DictFactoryImpl implements DictFactory<String, String> {
    List<String> list = Arrays.asList("A", "B", "C", "D");

    @Override
    public String get(String s, String k) {
        return list.get(RandomUtils.nextInt(0, 3));
    }
}
```
