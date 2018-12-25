# Spring IOC

#### Annotation

> 使用注解需要在 xml 中配置包扫描

```
<context:component-scan base-package="包名"></context:component-scan>
```

Spring 提供如下几个 Annotation 来标注 Spring Bean:

- `@Configuration`: 标注配置类
  > 相当于 xml 文件
- `@Bean`标注配置类里方法
  > 方法返回的的对象将由 spring 容器管理
- `@Component`: 标注一个普通的 Spring Bean 类
- `@Controller`: 标注一个控制器组件类
- `@Service`: 标注一个业务逻辑组件类
- `@Repository`: 标注一个 DAO 组件类

注入依赖的对象：

- `@Resource`

  > @Resource 不仅可以修饰 setter 方法，也可以直接修饰实例变量

- `@Autowired`

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
