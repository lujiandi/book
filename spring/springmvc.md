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

#### SpringMVC 处理 Form 表单提交 restful 请求

> Form 表单只支持 GET 和 Post 方式，添加`org.springframework.web.filter.HiddenHttpMethodFilter`过滤器可以让 Form 表单提交`PUT和DELETE`请求，需要在 Form 表单添加`_method`参数。

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
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filterCharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>


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

#### SpringMVC 配置

`@Value:`

> 在`Controller`中使用`@Value("${key}")` 需要 SpringMVC 单独加载配置文件。

```
 <context:property-placeholder location="classpath:properties/system.properties"></context:property-placeholder>
```

`开启SpringMVC注解驱动:`

> `<mvc:annotation-driven />` 会自动注册 `DefaultAnnotationHandlerMapping` 与 `AnnotationMethodHandlerAdapter` 两个 bean,是 SpringMVC 为`@Controller` 分发请求所必须的。
> 并提供了：数据绑定支持，@NumberFormatannotation 支持，@DateTimeFormat 支持，@Valid 支持，读写 XML 的支持（JAXB），读写 JSON 的支持（Jackson）。

```
<mvc:annotation-driven></mvc:annotation-driven>
```

`处理静态资源:`

> 优雅 REST 风格的资源 URL 不希望带 .html 或 .do 等后缀.由于早期的 Spring MVC 不能很好地处理静态资源，所以在 web.xml 中配置 DispatcherServlet 的请求映射，往往使用 _.do 、 _.xhtml 等方式。这就决定了请求 URL 必须是一个带后缀的 URL，而无法采用真正的 REST 风格的 URL。如果将 DispatcherServlet 请求映射配置为"/"，则 Spring MVC 将捕获 Web 容器所有的请求，包括静态资源的请求，SpringMVC 会将它们当成一个普通请求处理，因此找不到对应处理器将导致错误。

1. `<mvc:default-servlet-handler />`

   > 会在 SpringMVC 上下文中定义一个 `org.springframework.web.servlet.resource.DefaultServletHttpRequestHandler`，它会像一个检查员，对进入 `DispatcherServlet` 的 URL 进行筛查，如果发现是静态资源的请求，就将该请求转由 Web 应用服务器默认的 `Servlet` 处理，如果不是静态资源的请求，才由 `DispatcherServlet` 继续处理。一般 Web 应用服务器默认的 `Servlet` 名称是"default"，因此 `DefaultServletHttpRequestHandler` 可以找到它。如果你所有的 Web 应用服务器的默认 `Servlet` 名称不是"default"，则需要通过 `default-servlet-name` 属性显示指定：`<mvc:default-servlet-handler default-servlet-name="所使用的 Web 服务器默认使用的 Servlet 名称" />`

2. `<mvc:resources />`

   > `<mvc:default-servlet-handler />`将静态资源的处理经由 SpringMVC 框架交回 Web 应用服务器处理。而`<mvc:resources />`更进一步，由 SpringMVC 框架自己处理静态资源，并添加一些有用的附加值功能。

   > `<mvc:resources />`允许静态资源放在任何地方，如 `WEB-INF` 目录下、类路径下等，你甚至可以将 JavaScript 等静态文件打到 JAR 包中。通过 location 属性指定静态资源的位置，由于 `location` 属性是 `Resources` 类型，因此可以使用诸如`"classpath:"`等的资源前缀指定资源位置。传统 Web 容器的静态资源只能放在 Web 容器的根路径下，`<mvc:resources />`完全打破了这个限制。

   > 在 Maven Webapp 工程中`src/main/webapp`是 Web Resource Directory.

   ```
     <mvc:resources mapping="/css/**" location="/css/"></mvc:resources>
     <mvc:resources mapping="/js/**" location="/js/"></mvc:resources>
   ```

`返回JSP页面:`

> Controller 方法处理完成后返回 `String` 字符串默认指向`视图页面`;`数据`可以保存在`org.springframework.ui.Model`对象中,在页面中通过 EL 表达式(`${key}`)获取;`重定向`和`转发`是返回`"forward:"或者"redirect:"加上映射路径`。

```
<!-- jsp显示ViewResolver -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"></property>
        <property name="prefix" value="/WEB-INF/jsp/"></property>
        <property name="suffix" value=".jsp"></property>
</bean>
```

#### SpringMVC 拦截器

> 实现`org.springframework.web.servlet.HandlerInterceptor`接口

`拦截器链：`

![拦截器链](images/springmvc/SpringMvc拦截器链.png)

`拦截器映射路径：`

![拦截器链](images/springmvc/拦截器Mapping路径.png)

`拦截器配置例子:`

> 按顺序执行,可以对映射 url 进行排除。

```
<mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <mvc:exclude-mapping path="/login/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/css/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/images/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/js/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/api/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/upload/**"></mvc:exclude-mapping>
            <bean class="com.imooc.web.interceptor.SessionInterceptor"></bean>
        </mvc:interceptor>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <mvc:exclude-mapping path="/login/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/index"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/session/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/css/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/images/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/js/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/api/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/upload/**"></mvc:exclude-mapping>
            <bean class="com.imooc.web.interceptor.AuthInterceptor"></bean>
        </mvc:interceptor>
</mvc:interceptors>
```

#### SpringMVC 相关注解

`@org.springframework.stereotype.Controller`

> 注解类

`org.springframework.web.bind.annotation.RestController`

> 注解类

`@org.springframework.web.bind.annotation.RequestMapping`

> 注解类或者方法,可以指定映射路径和请求的类型

`@org.springframework.web.bind.annotation.RequestParam`

> 注解参数，通过`?key=value`方式传值。

`@org.springframework.web.bind.annotation.PathVariable`

> 注解参数,获取 url 对应占位符上的值。

```
   @RequestMapping(value = "/detail/comment/{pageNum}/{id}", method = RequestMethod.GET)
    public CommentListDto commentList(@PathVariable("pageNum") Integer pageNum,
                                      @PathVariable("id") Integer id) {
        return businessService.getCommentList(id, pageNum + 1, 10);

    }

```

`org.springframework.web.bind.annotation.ResponseBody`

> 将 Controller 的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到 response 对象的 body 区，通常用来返回 JSON 数据或者是 XML.在使用此注解之后不会再走试图处理器，而是直接将数据写入到输入流中，他的效果等同于通过 response 对象输出指定格式的数据.

`@org.springframework.web.bind.annotation.ControllerAdvice`

> 配合`org.springframework.web.bind.annotation.ExceptionHandler`进行全局异常处理。

```
@ControllerAdvice
@Slf4j
public class ExceptionResolver {
    public static final String ERROR_VIEW_NAME = "system/error";
    // 可以返回 ModelAndView
    @ExceptionHandler(value = Exception.class)
    public ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Exception e) {

        log.error("哪里出错了...", e);
        if (isAjax(request)) {
            ModelAndView jsonView = new ModelAndView(new MappingJackson2JsonView());
            jsonView.addObject("url", request.getRequestURI());
            jsonView.addObject("errMsg", e.getMessage());
            return jsonView;
        } else {
            ModelAndView modelAndView = new ModelAndView();
            modelAndView.addObject("url", request.getRequestURI());
            modelAndView.addObject("errMsg", e.getMessage());
            modelAndView.setViewName(ERROR_VIEW_NAME);
            return modelAndView;
        }
    }

    public boolean isAjax(HttpServletRequest request) {
        return (request.getHeader("X-Requested-With") != null
                && "XMLHttpRequest"
                .equals(request.getHeader("X-Requested-With").toString()));
    }
}
```
