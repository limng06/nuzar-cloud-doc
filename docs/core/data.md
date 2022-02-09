# 数据持久化

## nuzar-web-starter

配置数据源 `datasource`

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://10.122.0.56:3306/TEST?useUnicode=true&characterEncoding=utf8
    username: root
    password: Nuzar123456_
    hikari:
      validation-timeout: 60000
      max-lifetime: 360000
      maximum-pool-size: 10
```

## 实例

创建一个新maven项目，使用pom管理，可以使用lombok用于简化代码编写

* lombok请自行引入，scope请选择compile，仅在编译测试时生效

在`pom.xml`引入核心包

```xml
<properties>
    <nuzar.cloud.version>2.6.1-SNAPSHOT</nuzar.cloud.version>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
</properties>

<dependencies>
    <dependency>
        <groupId>com.nuzar</groupId>
        <artifactId>nuzar-core</artifactId>
        <version>${nuzar.cloud.version}</version>
    </dependency>
        <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>RELEASE</version>
        <scope>compile</scope>
    </dependency>
</dependencies>
```

添加`Product.java`，请继承基类BaseEntity

```java
@Data
@Table(name = "product")
public class Product extends BaseEntity implements Serializable {
    private static final long serialVersionUID = 1L;
    /**
     * 主键ID
     */
    @TableId
    @UniqueKey
    private String id;

    /**
     * 品牌
     */
    @Size(max = 20)
    @NotEmpty(message = "品牌不能为空")
    private String brand;

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

添加`ProductMapper.java`，CommonMapper中包含了常用的增删改查操作和简单JOIN表操作

```java
@Mapper
public interface ProductMapper extends CommonMapper<Product> {

}
```

添加`ProductService.java`，扩展CommonService接口，在此添加独有的接口

```java
public interface ProductService extends CommonService<Product> {
}
```

添加`ProductServiceImpl.java`，实现ProductService，继承CommonServiceImpl获得基础的数据操作能力

```java
@Service
public class ProductServiceImpl extends CommonServiceImpl<ProductMapper, Product> implements ProductService {

}
```

使用简单JOIN表关联查询, CommonMapper扩展了CommonJoinMapper接口，CommonJoinMapper中包含有JOIN查询接口

```java
/**
* 
*/
public ProductInfo getProductInfo(String id) {
    JoinLambdaWrapper<Product> wrapper = JoinWrappers.lambdaJoin();
    wrapper.selectAll(Product.class)
            .select(ProductExtend::getExtendName)
            .leftJoin(ProductExtend.class, ProductExtend::getProductId, Product::getId)
            .eq(Product::getId, id);
    ProductInfo info = baseMapper.selectJoinOne(ProductInfo.class, wrapper);
    return info;
}
```

## 数据校验

### 唯一校验

数据唯一校验，通过配置`@UniqueKey`实现在新增和更新时自动校验数据库中是否已经存在重复字段
调用方式1 CommonService.save(T t) 方法会自动检查
调用方式2 通过CommonService.checkIfExist(T t)主动检查

```json
{
    "time_stamp": "2022-02-09 14:40:45",
    "status": "500",
    "result": -1,
    "data": null,
    "error": "COMM_007",
    "error_description": "[id:2]已存在",
    "path": "/api/product",
    "version": "NC_V1"
}
```

### 字段校验

1. 使用@Validated校验
2. ValidatorUtils.validate(product);

```java
public T create(@RequestBody @Validated T entity) {
    if (getService().save(entity)) {
        return entity;
    }
    throw BizExceptionGenerator.generateBizException(CommonExceptionDefs.COMM_CREATE_ERROR);
}

public void valid() {
    Product product = new Product();
    ValidatorUtils.validate(product);
}
```

```java
/**
    * 品牌
    */
@Size(max = 20)
@NotEmpty(message = "品牌不能为空")
private String brand;
```

```json
{
    "time_stamp": "2022-02-08 17:34:44",
    "status": "500",
    "result": -1,
    "data": null,
    "error": "validation_failed",
    "error_description": "[brand]品牌不能为空",
    "path": "/api/product",
    "version": "NC_V1"
}
```

| @Annotation | 描述 |
| --- | --- |
| @AssertFalse | 被注释的元素必须为 | false |
| @AssertTrue | 同@AssertFalse |
| @DecimalMax | 被注释的元素必须是一个数字，其值必须小于等于指定的最大值 |
| @DecimalMin | 同DecimalMax |
| @Digits | 带批注的元素必须是一个在可接受范围内的数字 |
| @Email | 顾名思义 |
| @Future | 将来的日期 |
| @FutureOrPresent | 现在或将来 |
| @Max | 被注释的元素必须是一个数字，其值必须小于等于指定的最大值 |
| @Min | 被注释的元素必须是一个数字，其值必须大于等于指定的最小值 |
| @Negative | 带注释的元素必须是一个严格的负数（0为无效值） |
| @NegativeOrZero | 带注释的元素必须是一个严格的负数（包含0） |
| @NotBlank | 同StringUtils.isNotBlank |
| @NotEmpty | 同StringUtils.isNotEmpty |
| @NotNull | 不能是Null |
| @Null | 元素是Null |
| @Past | 被注释的元素必须是一个过去的日期 |
| @PastOrPresent | 过去和现在 |
| @Pattern | 被注释的元素必须符合指定的正则表达式 |
| @Positive | 被注释的元素必须严格的正数（0为无效值） |
| @PositiveOrZero | 被注释的元素必须严格的正数（包含0） |
| @Szie | 带注释的元素大小必须介于指定边界（包括）之间  |
