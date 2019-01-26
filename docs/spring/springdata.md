# Spring Data

> Spring Data 作为 SpringSource 的其中一个子项目，旨在统一和简化对各类型持久化存储和访问，而不拘泥于是关系型数据库还是 NoSQL 数据存储，使得对数据库的访问变得方便快捷，并支持 MapReduce 框架及云计算服务；对于拥有海量数据的项目，可以用 Spring Data 来简化项目的开发，就如 Spring Framework 对 JDBC、ORM 的支持一样，Spring Data 会让数据的访问变得更加方便，极大提高开发效率

#### Spring Data JPA

`maven依赖:`

```
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-jpa</artifactId>
    <version>1.8.0.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-entitymanager</artifactId>
    <version>4.3.6.Final</version>
</dependency>
```

`spring配置：`

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:jpa="http://www.springframework.org/schema/data/jpa"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/data/jpa http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

    <!--数据源-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="username" value="${user}"></property>
        <property name="password" value="${password}"></property>
        <property name="url" value="${jdbcUrl}"></property>
        <property name="driverClassName" value="${driverClass}"></property>
        <property name="initialSize" value="5"></property>
        <property name="minIdle" value="5"></property>
        <property name="maxActive" value="20"></property>
        <property name="maxWait" value="60000"></property>
        <property name="timeBetweenEvictionRunsMillis" value="60000"></property>
        <property name="minEvictableIdleTimeMillis" value="300000"></property>
        <property name="validationQuery" value="SELECT 1 FROM DUAL"></property>
        <property name="testWhileIdle" value="true"></property>
        <property name="testOnBorrow" value="false"></property>
        <property name="testOnReturn" value="false"></property>
        <property name="poolPreparedStatements" value="true"></property>
        <property name="maxPoolPreparedStatementPerConnectionSize" value="20"></property>
        <property name="filters" value="stat"></property>
        <property name="connectProperties">
            <props>
                <prop key="druid.stat.mergeSql">true</prop>
                <prop key="druid.stat.slowSqlMillis">500</prop>
            </props>
        </property>


    </bean>
    <!--2 配置EntityManagerFactory-->
    <bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="jpaVendorAdapter">
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter"/>
        </property>
        <property name="packagesToScan" value="com.imooc"/>
        <property name="jpaProperties">
            <props>
                <prop key="hibernate.ejb.naming_strategy">org.hibernate.cfg.ImprovedNamingStrategy</prop>
                <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5InnoDBDialect</prop>
                <prop key="hibernate.show_sql">true</prop>
                <prop key="hibernate.format_sql">true</prop>
                <prop key="hibernate.hbm2ddl.auto">update</prop>
            </props>
        </property>
    </bean>
    <!--3 配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactory"></property>
    </bean>
    <!--4 配置支持注解的事务-->
    <tx:annotation-driven transaction-manager="transactionManager"/>

    <!--5 配置spring data jpa-->
    <jpa:repositories base-package="com.imooc" entity-manager-factory-ref="entityManagerFactory"></jpa:repositories>


</beans>
```

`注解实体类：`

> @javax.persistence.Entity

`关于org.springframework.data.repository.Repository接口：`

- Repository 没有包含方法声明，是一个标记接口。
- 自定义接口继承 Repository 接口，将由 spring 管理;可以使用`@org.springframework.data.repository.RepositoryDefinition`注解代替继承方式。
- 接口继承关系：JpaRepository>>PagingAndSortingRepository>>CrudRepository>>Repository。
-

#### Spring Data Redis
