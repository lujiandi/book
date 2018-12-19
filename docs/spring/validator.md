### 使用 spring validator 校验数据

#### JSR-303

JSR-303 是 Java 为 Bean 数据合法性校验提供的标准框架，它定义了一套可标注在成员变量，属性方法上的校验注解。

Hibernate Validation 提供了这套标准的实现，在 springboot 工程里 spring-boot-starter-web 或者 Spring boot starter validation 的时候，默认会引入 hibernate-validator。

`javax.validation.Valid` 注解参数对象

`org.springframework.validation.annotation.Validated` 注解参数对象(和上面的注解效果一样)

`org.springframework.validation.BindingResult` 对象接收被@Valid 校验不通过的错误信息

示例代码：

> 实体类

```
package com.imooc.validator.model;

import com.imooc.validator.annotation.IsMobile;
import org.hibernate.validator.constraints.Email;
import org.hibernate.validator.constraints.NotBlank;

import javax.validation.constraints.Min;

public class Girl {

    @NotBlank(message = "姓名不能为空串")
    private String name;


    @Min(value = 18, message = "age要大于18")
    private Integer age;


    @IsMobile()
    private String phone;

    @Email(message = "email不正确")
    private String email;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

> 通过注解校验

```

    // @Valid 这个是用来接收参数的时候验证的，将验证结果存入 BindingResult 结果中
    // http://localhost:8080/valid/test1?name=assds&age=16
    @RequestMapping(value = "/test1")
    public Girl test(@Validated Girl girl, BindingResult bindingResult) throws Exception {

        if (bindingResult.hasErrors()) {
            log.info(bindingResult.getFieldError().getDefaultMessage());
            throw new Exception(bindingResult.getFieldError().getDefaultMessage());
        }
        return girl;
    }

```

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

#### 自定义注解

完成一些自定义的校验功能。

需要定义一个注解，还有一个完成校验功能的类(实现`javax.validation.ConstraintValidator`接口)

示例代码：

> IsMobile 注解

```
package com.imooc.validator.annotation;

import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.Documented;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.ElementType.PARAMETER;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER })
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = {IsMobileValidator.class}) //注解的实现类。
public @interface IsMobile {

    //校验错误的默认信息
    String message() default "手机号码格式有问题";

    //是否强制校验
    boolean isRequired() default false;

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};


}

```

> IsMobileValidator 完成校验手机功能

```
package com.imooc.validator.annotation;

import com.imooc.validator.annotation.IsMobile;
import org.apache.commons.lang3.StringUtils;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * Created by Administrator on 2018/12/8 0008.
 */
public class IsMobileValidator implements ConstraintValidator<IsMobile,String> {
    private boolean required = false;

    private static final Pattern mobile_pattern = Pattern.compile("1\\d{10}");

    //工具方法，判断是否是手机号
    public static boolean isMobile(String src) {
        if (StringUtils.isEmpty(src)) {
            return false;
        }
        Matcher m = mobile_pattern.matcher(src);
        return m.matches();
    }

    @Override
    public void initialize(IsMobile constraintAnnotation) {
        required = constraintAnnotation.isRequired();
    }

    @Override
    public boolean isValid(String phone, ConstraintValidatorContext constraintValidatorContext) {
        //是否为手机号的实现
        if (required) {
            return isMobile(phone);
        } else {
            if (StringUtils.isEmpty(phone)) {
                return true;
            } else {
                return isMobile(phone);
            }
        }
    }
}

```
