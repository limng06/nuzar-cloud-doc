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

## 构建实体类

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
    private String id;

    /**
     * 品牌
     */
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
