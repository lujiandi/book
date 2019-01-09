# SpringMVC

#### SpringMVC 工作流程

![SpringMVC工作流程](images/springmvc/SpringMVC工作流程.png)

> `org.springframework.web.servlet.DispatcherServlet` 其实就是个 Servlet ,需要在`web.xml`配置文件下声明,把希望由 DispatcherServlet 处理的请求映射到对应的 URL 上。

> 默认配置文件`<servletName>-serlvet.xml`放在`WEB-INF`目录下;可以通过`contextConfigLocation`参数改变文件指向。

`web.xml配置例子:`

```
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servletDispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/spring-web.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

#### SpringMVC 动态概念:

![SpringMVC动态概念](images/springmvc/springmvc动态概念.jpg)

#### SpringMVC 上下文

- `Root WebApplicationContext`
- `WebApplicationContext for namespace 'servletName'`

![SpringMVC 上下文](images/springmvc/SpringMvc上下文层级.jpg)

`在web.xml配置Root WebApplicationContext:`

```
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring/applicationContext.xml</param-value>
</context-param>
```

#### SpringMVC 拦截器

拦截器链：

![拦截器链](images/springmvc/SpringMvc拦截器链.png)

拦截器映射路径：

![拦截器链](images/springmvc/拦截器Mapping路径.png)

#### SpringMVC 处理 Form 表单提交 restful 请求

> Form 表单只支持 GET 和 Post 方式

![SpringMVC处理form表单提交restful请求](images/springmvc/SpringMVC处理form表单提交restful请求.png)

#### SpringMVC 处理 Form 表单提交带文件上传的 restful 请求

> Form 表单的 `enctype="multipart/form-data"`;需要先获取文件输入流,再解析表单参数。`org.springframework.web.multipart.support.MultipartFilter`的顺序在前， `org.springframework.web.filter.HiddenHttpMethodFilter`在后。

![SpringMVC处理form表单提交restful文件请求](images/springmvc/SpringMVC处理form表单提交restful文件请求.png)

`CommonsMultipartResolver`:

> 放在 Spring 的根上下文;`org.springframework.web.multipart.support.MultipartFilter#lookupMultipartResolver()`方法将从根上下文获取`CommonsMultipartResolver`,获取不到则使用默认的`org.springframework.web.multipart.support.StandardServletMultipartResolver`

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
