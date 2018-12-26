# SpringMVC

#### SpringMVC 工作流程

![SpringMVC工作流程](images/springmvc/SpringMVC工作流程.png)

SpringMVC 动态概念:

![SpringMVC动态概念](images/springmvc/springmvc动态概念.jpg)

SpringMVC 上下文:

![SpringMVC 上下文](images/springmvc/SpringMvc上下文层级.jpg)

#### SpringMVC 拦截器

拦截器链：

![拦截器链](images/springmvc/SpringMvc拦截器链.png)

拦截器映射路径：

![拦截器链](images/springmvc/拦截器Mapping路径.png)

#### SpringMVC 处理 form 表单提交 restful 请求

![SpringMVC处理form表单提交restful请求](images/springmvc/SpringMVC处理form表单提交restful请求.png)

SpringMVC 处理 form 表单提交带文件上传的 restful 请求:

![SpringMVC处理form表单提交restful文件请求](images/springmvc/SpringMVC处理form表单提交restful文件请求.png)

`multipartResolver`:

> 放在 Spring 的根上下文

```
<bean name="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <property name="maxUploadSize" value="209715200"></property>
    <property name="defaultEncoding" value="UTF-8"></property>
    <property name="resolveLazily" value="true"></property>
</bean>

```

`web.xml`:

```
<!--解决form表单提交restful请求带有文件上传 enctype="multipart/form-data"   -->
    <filter>
        <filter-name>MultipartFilter</filter-name>
        <filter-class>org.springframework.web.multipart.support.MultipartFilter</filter-class>
        <init-param>
            <param-name>multipartResolverBeanName</param-name>
            <param-value>multipartResolver</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>MultipartFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--springmvc配置用于form表单提交restful -->
    <!--使用ajax 不需要配置 -->
    <filter>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

```
