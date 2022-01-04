# 接口API

## nuzar-web-starter

添加 `ProductController.java`

```java
@RestController
@LogRecord(module = "test:product")
@PreAuth(prefix = "test:product")
@RequestMapping("/test/product")
public class ProductController extends CommonController<Product, ProductService, ProductController.ProductQuery> {

    /**
     * 快速检索类
     */
    public static class ProductQuery extends QueryModel {
        @QueryParam(filterType = QueryParam.FilterType.LIKE)
        private String brand;
    }
}
```

## 构建说明

框架默认提供了多个基础Controller类，大家可以自由组合使用

`CommonController` 提供查询和增删改查功能
`CommonSaveController` 提供增删改查功能
`CommonQueryControler` 提供查询功能
`BaseDictController` 提供字段功能

也可以更进一步直接使用更底层的Base前缀的接口

## 效果

![avatar](../images/product-api.png)
