# Spring IOC

#### Spring 注解

> 使用注解需要在 xml 中配置包扫描

```
<context:component-scan base-package="包名"></context:component-scan>
```

Spring 提供如下几个 Annotation 来标注 Spring Bean:

- `@Configuration`: 标注配置类
  > 相当于 xml 文件
- `@Bean`标注配置类里方法
  > 方法返回的的对象将由 spring 容器管理，参数对象根据类型从 Spring 容器中获得。
- `@Component`: 标注一个普通的 Spring Bean 类
- `@Controller`: 标注一个控制器组件类
- `@Service`: 标注一个业务逻辑组件类
- `@Repository`: 标注一个 DAO 组件类

注入依赖的对象：

- `@Resource`

  > @Resource 不仅可以修饰 setter 方法，也可以直接修饰实例变量

- `@Autowired`

  > 可以修饰实例变量和方法 ，`require=false` 表明参数对象不是必需的(即 Spring 容器没有参数类型的对象则不会调用被注解的方法)，

- `@Value`
  > 获得配置文件中 key 对应的值， `@Value("${key:默认值}")`

#### 为 bean 的属性注入静态常量

> `org.springframework.beans.factory.config.FieldRetrievingFactoryBean`

```
<bean id="类名.静态常量名" class="org.springframework.beans.factory.config.FieldRetrievingFactoryBean"></bean>

```

> 用非静态的 Setter 方法可以给 bean 的静态变量注入值

#### Spring 读取 properties 配置文件

> `${key}` 获取 key 对应的值

```
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <list>
                <value>classpath:db.properties</value>
                <value>classpath:redis.properties</value>
            </list>
        </property>
    </bean>
```

#### `org.springframework.core.io.Resource`

> bean 某些属性是 Resource 类型，Spring 能自动将 url 转成 Resource。

#### `org.springframework.core.io.ResourceLoader`

> 能够将`classpath,file,http`路径的文件加载成 Resource 对象

#### 获取 ApplicationContext 对象

> 实现 ApplicationContextAware 接口

```
@Component
public class ApplicationContextHolder implements ApplicationContextAware {

    private static ApplicationContext ctx;

    public static ApplicationContext getContext(){
        return ctx;
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        System.out.println("ApplicationContextHolder execute...setApplicationContext()");
        ctx = applicationContext;
    }
}
```

> 通过 classpath 下的 xml 获取 ApplicationContext

```
ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("/spring.xml");

```

#### 定时任务

> 开启注解驱动，`@org.springframework.scheduling.annotation.Scheduled`注解任务方法。

```
<task:annotation-driven></task:annotation-driven>
```
