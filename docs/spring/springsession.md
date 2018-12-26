# SpringSession

> maven 依赖：

```
<!-- 使用redis存放session -->
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-data-redis</artifactId>
    <version>1.2.0.RELEASE</version>
</dependency>
```

#### 在 web.xml 中 注册`org.springframework.session.web.http.SessionRepositoryFilter`,

> sessionRepositoryFilter 实例对象存在于 Spring 容器中。

```
<filter>
    <filter-name>springSessionRepositoryFilter</filter-name>
    <filter-class>org.springframework.web.filteDelegatingFilterProxy</filter-class>
</filter>
<filter-mapping>
    <filter-name>springSessionRepositoryFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

```

#### `org.springframework.session.config.annotation.web.http.SpringHttpSessionConfiguration`

> 是一个配置类，其中`springSessionRepositoryFilter()方法`返回`SessionRepositoryFilter类`的实例对象

```
@Configuration
public class SpringHttpSessionConfiguration {

@Bean
public <S extends ExpiringSession> SessionRepositoryFilter<? extends ExpiringSession>springSessionRepositoryFilter(
		SessionRepository<S> sessionRepository) {
	SessionRepositoryFilter<S> sessionRepositoryFilter = new SessionRepositoryFilter<S>(
			sessionRepository);
	sessionRepositoryFilter.setServletContext(this.servletContext);
	if (this.httpSessionStrategy instanceof MultiHttpSessionStrategy) {
		sessionRepositoryFilter.setHttpSessionStrategy(
				(MultiHttpSessionStrategy) this.httpSessionStrategy);
	}
	else {
		sessionRepositoryFilter.setHttpSessionStrategy(this.httpSessionStrategy);
	}
	return sessionRepositoryFilter;
}

}

```

#### `org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration` 配置

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.1.xsd">


    <context:annotation-config/>

    <!--JSON序列化-->
    <bean id="genericJackson2JsonRedisSerializer"
          class="org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer"></bean>

    <!--
    父类
    org.springframework.session.config.annotation.web.http.SpringHttpSessionConfiguration
    定义了bean(sessionRepositoryFilter)
    -->
    <bean id="redisHttpSessionConfiguration"
          class="org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration">
        <property name="maxInactiveIntervalInSeconds" value="1800"/>
        <!--序列化方式-->
        <!--默认使用jdk序列化 对象要实现 Serializable接口-->
        <property name="defaultRedisSerializer" ref="genericJackson2JsonRedisSerializer"></property>
    </bean>

    <!--用于获取session的cookie 自动注入-->
    <!--org.springframework.session.config.annotation.web.http.SpringHttpSessionConfiguration.setCookieSerializer  -->
    <bean id="defaultCookieSerializer" class="org.springframework.session.web.http.DefaultCookieSerializer">
        <property name="cookieName" value="SESSION_NAME"/>
        <property name="domainName" value=".lujiandi.top"/>
        <property name="useHttpOnlyCookie" value="true"/>
        <property name="cookiePath" value="/"/>
        <property name="cookieMaxAge" value="31536000"/>
    </bean>


    <!--实现RedisConnectionFactory-->
    <!--org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration.sessionRedisTemplate-->
    <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <property name="hostName" value="127.0.0.1"/>
        <property name="port" value="6379"/>
        <property name="poolConfig">
            <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
                <property name="maxTotal" value="20"/>
            </bean>
        </property>
    </bean>
</beans>
```
