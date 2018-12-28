# Spring 整合 Hiernate

maven 依赖：

```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.20</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>4.3.6.Final</version>
</dependency>
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-ehcache</artifactId>
    <version>4.3.6.Final</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>4.1.3.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifad
    <version>4.1.3.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>4.1.3.RELEASE</version>
</dependency>
```

Spring 配置：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.1.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.1.xsd">

    <!--1 配置数据源 -->
    <context:property-placeholder location="classpath:db.properties"/>
    <bean id="dataSource"
          class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="username" value="${user}"></property>
        <property name="password" value="${password}"></property>
        <property name="url" value="${jdbcUrl}"></property>
        <property name="driverClassName" value="${driverClass}"></property>
    </bean>

    <bean id="sessionFactory"
          class="org.springframework.orm.hibernate4.LocalSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="hibernateProperties">
            <props>
                <prop key="hibernate.dialect">org.hibernate.dialect.MySQLDialect</prop>
                <prop key="hibernate.show_sql">true</prop>
                <prop key="hibernate.format_sql">true</prop>
                <prop key="hibernate.hbm2ddl.auto">update</prop>
                <!-- <prop key="hibernate.hbm2ddl.auto">create</prop> -->
                <!-- hibernate二级缓存 -->
                <prop key="hibernate.cache.use_second_level_cache">true</prop>
                <prop key="hibernate.cache.use_query_cache">true</prop>
                <prop key="hibernate.cache.region.factory_class">org.hibernate.cache.ehcache.EhCacheRegionFactory</prop>
            </props>
        </property>
        <!-- 扫描有注解的实体类的包 -->
        <property name="packagesToScan" value=""></property>
    </bean>


    <!-- hibernateTemplate  封装了增删改查的方法 -->
    <!-- 另一种方式 dao继承 HibernateDaoSupport (注入sessionFactory)-->
    <bean id="hibernateTemplate" class="org.springframework.orm.hibernate4.HibernateTemplate">
        <property name="sessionFactory" ref="sessionFactory"></property>
    </bean>

    <!-- Hibernate事务管理器 -->
    <bean id="transactionManager" class="org.springframework.orm.hibernate4.HibernateTransactionManager">
        <property name="sessionFactory" ref="sessionFactory"></property>
    </bean>

    <!--基于aspectj的xml的方式的事务配置 begin-->
    <!-- 事务通知 -->
    <!--
    propagation:事务传播行为
    isolation：隔离级别
    -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="find*" read-only="true" propagation="SUPPORTS"/>
            <tx:method name="save*" propagation="REQUIRED" rollback-for="UnsupportedOperationException"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <aop:config>
        <!-- 切入点 -->
        <aop:pointcut id="xxxServiceMethods" expression="execution( * 包名.xxxService接口+.*(..) )"/>
        <!--advisor 只有一个advice增强-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="xxxServiceMethods"/>
    </aop:config>
    <!--基于aspectj的xml的方式的事务配置 end..............-->


    <!--基于注解的事务配置只需在类或方法上注解-->
    <!--<tx:annotation-driven transaction-manager="transactionManager"/>-->

    <bean id="ehCacheManagerFactoryBean" class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean">
        <property name="configLocation" value="classpath:ehcache.xml"/>
    </bean>

    <context:component-scan base-package=""></context:component-scan>
</beans>
```

#### 用注解标注实体类

```
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import org.hibernate.annotations.Cache;
import org.hibernate.annotations.CacheConcurrencyStrategy;
// hibernate注解可以写在字段属性或者get方法上
@Cache(usage=CacheConcurrencyStrategy.READ_WRITE,region="ehcacheName",include="all")
@Entity(name="seller")
public class Seller {
    @Id
    @GeneratedValue
	private Integer id;

    @Column(length=30)
	private String name;

    @Column(length=30)
	private String tel;

    @Column(length=50)
	private String address;

    @Column(length=30)
	private String website;

    @Column
	private Integer star;

	 @Column(length=30)
	private String business;

	public Seller() {
	}
	public Seller(String name, String tel, String address, String website,
			Integer star, String business) {
		this.name = name;
		this.tel = tel;
		this.address = address;
		this.website = website;
		this.star = star;
		this.business = business;
	}

	public Integer getId() {
		return this.id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getName() {
		return this.name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getTel() {
		return this.tel;
	}

	public void setTel(String tel) {
		this.tel = tel;
	}

	public String getAddress() {
		return this.address;
	}

	public void setAddress(String address) {
		this.address = address;
	}

	public String getWebsite() {
		return this.website;
	}

	public void setWebsite(String website) {
		this.website = website;
	}

	public Integer getStar() {
		return this.star;
	}

	public void setStar(Integer star) {
		this.star = star;
	}

	public String getBusiness() {
		return this.business;
	}

	public void setBusiness(String business) {
		this.business = business;
	}
	@Override
	public String toString() {
		return "Seller [id=" + id + ", name=" + name + ", tel=" + tel
				+ ", address=" + address + ", website=" + website + ", star="
				+ star + ", business=" + business + "]";
	}
}
```
