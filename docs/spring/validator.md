### 使用 spring validator 校验数据

#### JSR-303

JSR-303 是 Java 为 Bean 数据合法性校验提供的标准框架，它定义了一套可标注在成员变量，属性方法上的校验注解。

Hibernate Validation 提供了这套标准的实现，在 springboot 工程里 spring-boot-starter-web 或者 Spring boot starter validation 的时候，默认会引入 hibernate-validator。

`javax.validation.Valid` 注解参数对象

`org.springframework.validation.annotation.Validated` 注解参数对象(和上面的注解效果一样)

`org.springframework.validation.BindingResult` 对象接收被@Valid 校验不通过的错误信息

#### 常见的校验注解

> 用来注解对象属性

`@Null` 被注释的元素必须为 null

`@NotNull` 被注释的元素必须不为 null

`@AssertTrue` 被注释的元素必须为 true

`@AssertFalse` 被注释的元素必须为 false

`@Min(value)` 被注释的元素必须是一个数字，其值必须大于等于指定的最小值

`@Max(value)` 被注释的元素必须是一个数字，其值必须小于等于指定的最大值

`@DecimalMin(value)` 被注释的元素必须是一个数字，其值必须大于等于指定的最小值

`@DecimalMax(value)` 被注释的元素必须是一个数字，其值必须小于等于指定的最大值

`@Size(max=, min=)` 被注释的元素的大小必须在指定的范围内

`@Digits (integer, fraction)` 被注释的元素必须是一个数字，其值必须在可接受的范围内

`@Past` 被注释的元素必须是一个过去的日期

`@Future` 被注释的元素必须是一个将来的日期

`@Pattern(regex=,flag=)` 被注释的元素必须符合指定的正则表达式

#### Hibernate Validator 提供的校验注解：

`@NotBlank(message =)` 验证字符串非 null，且长度必须大于 0

`@Email` 被注释的元素必须是电子邮箱地址

`@Length(min=,max=)` 被注释的字符串的大小必须在指定的范围内

`@NotEmpty` 被注释的字符串的必须非空

`@Range(min=,max=,message=)` 被注释的元素必须在合适的范围内
