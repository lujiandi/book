# Spring Data

> Spring Data 作为 SpringSource 的其中一个子项目，旨在统一和简化对各类型持久化存储和访问，而不拘泥于是关系型数据库还是 NoSQL 数据存储，使得对数据库的访问变得方便快捷，并支持 MapReduce 框架及云计算服务；对于拥有海量数据的项目，可以用 Spring Data 来简化项目的开发，就如 Spring Framework 对 JDBC、ORM 的支持一样，Spring Data 会让数据的访问变得更加方便，极大提高开发效率

### Spring Data JPA

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

#### `关于org.springframework.data.repository.Repository接口`

- Repository 没有包含方法声明，是一个标记接口。
- 自定义接口继承 Repository 接口，将由 spring 管理;可以使用`@org.springframework.data.repository.RepositoryDefinition`注解代替继承方式。
- 接口继承关系：JpaRepository>>PagingAndSortingRepository(分页)>>CrudRepository(实现 CURD)>>Repository；自定义接口继承 JpaRepository 就可以实现 CURD 和分页功能。

#### `org.springframework.data.jpa.repository.JpaSpecificationExecutor接口`

- 继承这个接口后可以添加查询条件

#### `自定义的 Repository 接口`

- 方法声明需要按照一定的规则，缺点是难以实现复杂的查询。
- 使用`@org.springframework.data.jpa.repository.Query`注解方法不需要遵循命名规则，支持原生 sql 查询。
- `@org.springframework.data.jpa.repository.Modifying`配合`@Query`完成更新操作；需要事务管理。

`示例代码:`

```
public interface EmployeeRepository extends JpaSpecificationExecutor<Employee>,JpaRepository<Employee, Integer> {

    //	符合一定的规则
	public List<Employee>  findByName(String name);

	//不需要符合命名规则
	//HQL
	@Query("select o from Employee o where id=(select max(id) from Employee t1)")
    public Employee getEmployeeByMaxId();

	// 占位符 =?1 =?2
	@Query("select o from Employee o where o.name like %:name% ")
    public List<Employee> queryLike(@Param("name")String name);

    //原生sql查询
	@Query(nativeQuery = true, value = "select count(1) from employee")
    public long getCount();

	//需要事务管理
	@Modifying
	@Query(" update Employee o set o.age=:age where o.id=:id ")
	public int updateAge(@Param("id")Integer id,@Param("age")Integer age);


}
```

`分页查询:`

```
public Page<Employee> getPage(int pageNum, int pageSize){

	Order order = new Order(Direction.ASC, "id");
	Sort sort = new Sort(order);
    //page zero-based page index.
	// ctrl+h 查看子类 实例化
	Pageable pageable = new PageRequest(pageNum-1, pageSize,sort);
	return employeeRepository.findAll(pageable);
}
public Page<Employee> findPage(int pageNum, int pageSize,final String name){

    // 添加查询条件
    Specification<Employee> spec = new Specification<Employee>() {
    	@Override
    	public Predicate toPredicate(Root<Employee> root, CriteriaQuery<?> query,
    			CriteriaBuilder cb) {
    		Path path  = root.get("name");
    		return cb.like(path, "%"+name+"%");

    	}
    };
    Order order = new Order(Direction.ASC, "id");
    Sort sort = new Sort(order);
    Pageable pageable = new PageRequest(pageNum-1, pageSize, sort);
    return employeeRepository.findAll(spec, pageable);
}
```

### Spring Data Redis

`maven依赖:`

```
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>1.8.10.RELEASE</version>
</dependency>
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.9.0</version>
</dependency>
```

`spring配置：`

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--redis-->
    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <property name="maxTotal" value="${redis.max.total}"/>
        <property name="maxIdle" value="${redis.max.idle}"/>
        <property name="minIdle" value="${redis.min.idle}"/>
        <property name="maxWaitMillis" value="3000"/>
        <property name="testOnBorrow" value="${redis.test.borrow}"/>
        <property name="testOnReturn" value="true"/>
        <property name="blockWhenExhausted" value="true"/>
    </bean>
    <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <property name="poolConfig" ref="jedisPoolConfig"></property>
        <property name="port" value="${redis.port}"></property>
        <property name="hostName" value="${redis.ip}"></property>
    </bean>
    <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">
        <property name="connectionFactory" ref="jedisConnectionFactory"></property>
        <!--序列化方式-->
        <property name="keySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
        </property>
        <property name="valueSerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
        </property>
        <property name="hashKeySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
        </property>
        <property name="hashValueSerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
        </property>
    </bean>
</beans>
```

`封装redisTemplate:`

```
@Component
public class RedisOperator {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    // Key（键），简单的key-value操作


    /**
     * 实现命令：TTL key，以秒为单位，返回给定 key的剩余生存时间(TTL, time to live)。
     *
     * @param key
     * @return
     */

    public long ttl(String key) {

        return redisTemplate.getExpire(key);

    }


    /**
     * 实现命令：expire 设置过期时间，单位秒
     *
     * @param key
     * @return
     */

    public void expire(String key, long timeout) {

        redisTemplate.expire(key, timeout, TimeUnit.SECONDS);

    }


    /**
     * 实现命令：INCR key，增加key一次
     *
     * @param key
     * @return
     */

    public long incr(String key, long delta) {

        return redisTemplate.opsForValue().increment(key, delta);

    }


    /**
     * 实现命令：KEYS pattern，查找所有符合给定模式 pattern的 key
     */

    public Set<String> keys(String pattern) {

        return redisTemplate.keys(pattern);

    }


    /**
     * 实现命令：DEL key，删除一个key
     *
     * @param key
     */

    public void del(String key) {

        redisTemplate.delete(key);

    }


    // String（字符串）


    /**
     * 实现命令：SET key value，设置一个key-value（将字符串值 value关联到 key）
     *
     * @param key
     * @param value
     */

    public void set(String key, String value) {

        redisTemplate.opsForValue().set(key, value);

    }


    /**
     * 实现命令：SET key value EX seconds，设置key-value和超时时间（秒）
     *
     * @param key
     * @param value
     * @param timeout （以秒为单位）
     */

    public void set(String key, String value, long timeout) {

        redisTemplate.opsForValue().set(key, value, timeout, TimeUnit.SECONDS);

    }


    /**
     * 实现命令：GET key，返回 key所关联的字符串值。
     *
     * @param key
     * @return value
     */

    public String get(String key) {

        return (String) redisTemplate.opsForValue().get(key);

    }


    // Hash（哈希表）


    /**
     * 实现命令：HSET key field value，将哈希表 key中的域 field的值设为 value
     *
     * @param key
     * @param field
     * @param value
     */

    public void hset(String key, String field, Object value) {

        redisTemplate.opsForHash().put(key, field, value);

    }


    /**
     * 实现命令：HGET key field，返回哈希表 key中给定域 field的值
     *
     * @param key
     * @param field
     * @return
     */

    public String hget(String key, String field) {

        return (String) redisTemplate.opsForHash().get(key, field);

    }


    /**
     * 实现命令：HDEL key field [field ...]，删除哈希表 key 中的一个或多个指定域，不存在的域将被忽略。
     *
     * @param key
     * @param fields
     */

    public void hdel(String key, Object... fields) {

        redisTemplate.opsForHash().delete(key, fields);

    }


    /**
     * 实现命令：HGETALL key，返回哈希表 key中，所有的域和值。
     *
     * @param key
     * @return
     */

    public Map<Object, Object> hgetall(String key) {

        return redisTemplate.opsForHash().entries(key);

    }


    // List（列表）


    /**
     * 实现命令：LPUSH key value，将一个值 value插入到列表 key的表头
     *
     * @param key
     * @param value
     * @return 执行 LPUSH命令后，列表的长度。
     */

    public long lpush(String key, String value) {

        return redisTemplate.opsForList().leftPush(key, value);

    }


    /**
     * 实现命令：LPOP key，移除并返回列表 key的头元素。
     *
     * @param key
     * @return 列表key的头元素。
     */

    public String lpop(String key) {

        return (String) redisTemplate.opsForList().leftPop(key);

    }


    /**
     * 实现命令：RPUSH key value，将一个值 value插入到列表 key的表尾(最右边)。
     *
     * @param key
     * @param value
     * @return 执行 LPUSH命令后，列表的长度。
     */
    public long rpush(String key, String value) {

        return redisTemplate.opsForList().rightPush(key, value);

    }
}

```
