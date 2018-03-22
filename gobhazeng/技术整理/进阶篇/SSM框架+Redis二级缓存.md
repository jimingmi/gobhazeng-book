# SSM框架+Redis二级缓存

> github [项目源码](https://github.com/jimingmi/SSM_Redis)

## 一、整体架构

- 使用Ehcache与Mybatis整合，可以提高性能，降低数据库压力

- Spring管理Redis连接池，收权

- 实现RedisCache

## 二、项目实现

### 1. pom.xml中加入需要的Maven依赖

```xml
<!-- spring-redis -->
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>1.6.2.RELEASE</version>
</dependency>
<!-- redis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.8.0</version>
</dependency>
<!-- Ehcache -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.0.0</version>
</dependency>
```
### 2. 在applicationContext.xml(beans.xml)中引入redis配置

```xml
<!-- 引入数据库配置文件 -->
<bean id="propertyConfigurer"    class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="locations">
        <list>
            <value>classpath:jdbc.properties</value>
            <value>classpath:redis.properties</value>
        </list>
    </property>
</bean>
    <!-- 11.Redis -->
    <!-- 连接池配置 -->
    <bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <property name="maxIdle" value="${redis.maxIdle}" />
        <property name="testOnBorrow" value="${redis.testOnBorrow}" />
    </bean>
    <bean id="connectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <property name="hostName" value="${redis.host}" />
        <property name="port" value="${redis.port}" />
        <property name="password" value="${redis.pass}" />
        <property name="poolConfig" ref="poolConfig" />
    </bean>
    <bean id="redisTemplate" class=" org.springframework.data.redis.core.RedisTemplate">
        <property name="connectionFactory" ref="connectionFactory" />
        <property name="keySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />
        </property>
        <property name="valueSerializer">
            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer" />
        </property>
    </bean>
```

> - 读取配置文件需要注释原来的读取代码

> - applicationContext.xml(beans.xml)需要在`Spring Config Editor`视图中打开`p`

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710192208.png)

### 3. 创建缓存实现类RedisCache

```java
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;
import org.apache.ibatis.cache.Cache;


/*
 * 使用第三方缓存服务器，处理二级缓存
 */
public class RedisCache implements Cache {
	private final ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

	private String id;

	public RedisCache( final String id ) {
		if (id == null) {
			throw new IllegalArgumentException("Cache instances require an ID");
		}
		this.id = id;

	}

	@Override
	public String getId() {
		return id;
	}

	@Override
	public void putObject( Object key , Object value ) {
		JedisUtil.getJedis().set(SerializeUtil.serialize(key.toString()), SerializeUtil.serialize(value));

	}

	@Override
	public Object getObject( Object key ) {
		Object value = SerializeUtil.unserialize(JedisUtil.getJedis().get(SerializeUtil.serialize(key.toString())));
		return value;

	}

	@Override
	public Object removeObject( Object key ) {
		return JedisUtil.getJedis().expire(SerializeUtil.serialize(key.toString()), 0);

	}

	@Override
	public void clear() {
		JedisUtil.getJedis().flushDB();
	}

	@Override
	public int getSize() {
		return Integer.valueOf(JedisUtil.getJedis().dbSize().toString());
	}

	@Override
	public ReadWriteLock getReadWriteLock() {
		return readWriteLock;
	}

}
```

### 4. 创建JedisUtil连接池类

```java
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

public class JedisUtil {

	private static String	hostName	= "127.0.0.1";
	private static int		port		= 6379;
	private static String	password	= "";

	private static int	MaxTotal						= 300;
	private static int	MaxIdle							= 50;
	private static int	MinIdle							= 8;
	private static int	MaxWaitMillis					= 10000;
	private static int	TimeBetweenEvictionRunsMillis	= 30000;
	private static int	NumTestsPerEvictionRun			= 10;
	private static int	MinEvictableIdleTimeMillis		= 60000;
	private static int	TIMEOUT							= 10000;

	private static JedisPool jedisPool = null;

	static {
		try {
			JedisPoolConfig config = new JedisPoolConfig();
			config.setMaxTotal(MaxTotal);
			config.setMaxIdle(MaxIdle);
			config.setMinIdle(MinIdle);// 设置最小空闲数
			config.setMaxWaitMillis(MaxWaitMillis);
			config.setTestOnBorrow(true);
			config.setTestOnReturn(true);
			// Idle时进行连接扫描
			config.setTestWhileIdle(true);
			// 表示idle object evitor两次扫描之间要sleep的毫秒数
			config.setTimeBetweenEvictionRunsMillis(TimeBetweenEvictionRunsMillis);
			// 表示idle object evitor每次扫描的最多的对象数
			config.setNumTestsPerEvictionRun(NumTestsPerEvictionRun);
			// 表示一个对象至少停留在idle状态的最短时间，然后才能被idle object
			// evitor扫描并驱逐；这一项只有在timeBetweenEvictionRunsMillis大于0时才有意义
			config.setMinEvictableIdleTimeMillis(MinEvictableIdleTimeMillis);

			jedisPool = new JedisPool(config, hostName, port, TIMEOUT, password);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	public synchronized static Jedis getJedis() {
		try {
			if (jedisPool != null) {
				Jedis jedis = jedisPool.getResource();
				return jedis;
			} else {
				return null;
			}
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}

	public static void returnResource( final Jedis jedis ) {
		if (jedis != null) {
			jedisPool.close();
		}
	}

}
```

> 注意根据自身环境对hostName、port、password进行修改

### 5. 创建SerializeUtil序列化工具类

```java
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

public class SerializeUtil {
	public static byte[] serialize(Object object) {
		ObjectOutputStream oos = null;
		ByteArrayOutputStream baos = null;
		try {
			baos = new ByteArrayOutputStream();
			oos = new ObjectOutputStream(baos);
			oos.writeObject(object);
			byte[] bytes = baos.toByteArray();
			return bytes;
		} catch (Exception e) {
			e.printStackTrace();
		}
		return null;
	}

	public static Object unserialize(byte[] bytes) {
		if (bytes == null) {
			return null;
		}
		ByteArrayInputStream bais = null;
		try {
			// 反序列化
			bais = new ByteArrayInputStream(bytes);
			ObjectInputStream ois = new ObjectInputStream(bais);
			return ois.readObject();
		} catch (Exception e) {
			e.printStackTrace();
		}
		return null;
	}
}
```

### 6. 配置文件redis.properties

```
# Redis settings
redis.host=127.0.0.1
redis.port=6379
redis.pass=

redis.maxIdle=300
redis.maxActive=600
redis.maxWait=1000
redis.testOnBorrow=true
```

> 注意根据自身环境对host、port、pass进行修改

### 7. Mapper中加入MyBatis二级缓存

```xml
<mapper namespace="com.gobha.dao.UserDao" >
    <cache type="com.gobha.util.RedisCache"/>
</mapper>
```
> 设置`useCache="true"`

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710212228.png)

### 8. Mybatis(mybatis-config.xml)全局配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!-- 日志开启 -->
        <setting name="logImpl" value="LOG4J"/>
        <!-- 二级缓存开启 -->
        <setting name="cacheEnabled" value="true"/>
        <setting name="lazyLoadingEnabled" value="false"/>
        <setting name="aggressiveLazyLoading" value="true"/>
    </settings>

    <!-- 此项目需要的typeAliases -->
    <typeAliases>
        <package name="com.gobha.model"/>
    </typeAliases>

</configuration>
```

### 9. 项目测试

启动MySQL、Redis

将项目添加入本地tomcat容器

启动tomcat，浏览器访问`http://localhost:8001/SSM_Redis/`

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710161704.png)

输入账号：123  密码：123

跳转主页，查看控制台

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710212231.png)

Cache缓存命中为0，此时查询MySQL数据库

退出后再次登录，查看控制台

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710212233.png)

Cache出现命中，此时直接从Redis读取数据，不查询MySQL数据库，项目测试完毕