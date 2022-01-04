# 权限

## nuzar-web-starter

权限模块使用@PreAuth注解标注接口的权限标识，用户调用接口的时候会根据通过用户权限接口获取用户的接口标识集合
然后和接口的标识做匹配，如果没有权限则返回403

添加 `ProductController.java`

```java
@RestController
@PreAuth(prefix = "test:product")
@RequestMapping("/test/product")
public class ProductController {

    @PreAuth({"dict", "manage"})
    @RequestMapping(
        value = {"/dict"},
        method = {RequestMethod.GET}
    )
    public List<CommonDict> dict(@QueryBody ProductQuery query) {
        return DictUtils.from(this.getService().list(query.getQueryWrapper(this.getService().getEntityClass())), this.getService().getEntityClass());
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

以上接口的权限标识为 `test:product:dict`、 `test:product:manage`，如果用户持有该标识即表示拥有该接口标识。

## 扩展

用户可以通过扩展`PermissionProvider`接口实现自己的用户权限标识提供者

用户可以通过扩展`UserPermissionChecker`接口实现自己的接口权限校验逻辑
