# SSM框架

## 概念

- MyBatis：持久层框架，ORM框架，只是针对于数据库的操作

- SpringMVC：WEB层框架 ，是用来传递(客户端 <-> 服务端)和处理数据

- Spring ：IoC(DI) 和 AOP 的容器框架

- IOC 控制反转(实现方式 Java的反射机制)

- AOP 是面向切面编程 (实现方式Java的动态代理 - 使用的第三方的CGLIB动态代理)

## Spring + SpringMVC 整合 (放权)

SpringMVC本身就是Spring框架的一部分，所以整合相对容易

- springMVC.xml配置

```xml
    <!-- 1.启动SpringMVC注解 -->
    <mvc:annotation-driven/>
    <!-- 2.扫描 -->
    <context:component-scan base-package="com.gobha.**.web" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <context:include-filter type="annotation" expression="org.springframework.web.bind.annotation.ControllerAdvice"/>
    </context:component-scan>
    <!-- 3.JSP请求转发的视图解析器 -->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/"/>
        <!-- 后缀 -->
        <property name="suffix" value=".jsp"/>
        <!-- JSTL视图 -->
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
        <!-- 排序 -->
        <property name="order" value="10"/>
    </bean>
```

- beans.xml配置

```xml
    <!-- 1.启动Spring注解 -->
    <context:annotation-config/>
    <!-- 2.扫描 -->
    <context:component-scan base-package="com.gobha">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <context:exclude-filter type="annotation" expression="org.springframework.web.bind.annotation.ControllerAdvice"/>
    </context:component-scan>
```

- web.xml配置(包含解决POST中文乱码以及HTTP请求转换的Servlet过滤器)

```xml
    <!-- 1.解决POST请求的中文乱码问题 -->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
            <param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <!-- 2.HTTP请求转换的Servlet过滤器 -->
    <filter>
        <filter-name>httpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>httpMethodFilter</filter-name>
        <servlet-name>dispatcher</servlet-name>
    </filter-mapping>
    <!-- 3.配置DispatcherServlet读取自定义核心配置文件 -->
    <servlet>
        <servlet-name>springMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/springMVC.xml</param-value>
        </init-param>
        <load-on-startup>2</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!-- 4.配置监听器，读取Spring的核心配置文件 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:beans.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!-- 5.配置HttpServletRequest监听器,方便以后在任何层次都能获取ServletAPI -->
    <listener>
        <listener-class>org.springframework.web.context.request.RequestContextListener</listener-class>
    </listener>
    <!-- 6.配置Log4j的监听器 -->
    <context-param>
        <param-name>log4jConfigLocation</param-name>
        <param-value>classpath:log4j.properties</param-value>
    </context-param>

    <context-param>
        <param-name>log4jRefreshInterval</param-name>
        <!-- 6秒 -->
        <param-value>6000</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
    </listener>
    <!-- 7.Spring解决缓存异常问题的监听器 -->
    <listener>
        <listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
    </listener>
```

## Spring + MyBatis整合(收权)

**需要的JAR包**

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251309.png)

**收回连接数据源的权力**

- beans.xml配置

```xml
    <!-- 3.读取属性文件 -->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!-- 4.配置数据源 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
        <property name="driverClassName" value="${jdbc.mysql.driver}" />
        <property name="url" value="${jdbc.mysql.url}" />
        <property name="username" value="${jdbc.mysql.username}" />
        <property name="password" value="${jdbc.mysql.password}" />

        <!-- 配置初始化大小、最小、最大 -->
        <property name="initialSize" value="3" />
        <!-- 最大空闲时，当经过一个高峰之后，连接池可以将一些用不到的连接释放，一直减少到maxIdle为止 -->
        <property name="minIdle" value="5" />
        <!-- 连接池的最大值 -->
        <property name="maxActive" value="20" />
        <!-- 配置获取连接等待超时的时间 -->
        <property name="maxWait" value="60000" />
        <!-- 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 -->
        <property name="timeBetweenEvictionRunsMillis" value="60000" />
        <!-- 配置一个连接在池中最小生存的时间，单位是毫秒 -->
        <property name="minEvictableIdleTimeMillis" value="300000" />

        <property name="validationQuery" value="SELECT 'x'" />
        <property name="testWhileIdle" value="true" />
        <property name="testOnBorrow" value="false" />
        <property name="testOnReturn" value="false" />
    </bean>
```

**收回创建SqlSessionFactory的权力/读取核心配置文件/读取映射文件的权力/事务管理器**

- beans.xml配置

```xml
    <!-- 5.创建SqlSession工厂和读取核心配置文件以及映射文件 -->
    <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 配置数据源 -->
        <property name="dataSource" ref="dataSource"/>
        <!-- 配置读取核心配置文件 -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <!-- 配置读取映射文件 -->
        <property name="mapperLocations" value="classpath:com/gobha/**/model/*Mapper.xml"/>
    </bean>
    <!-- 6.动态创建接口实现类的工具类 sqlsession.getMapper方法 -->
    <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 实现接口的位置 -->
        <property name="basePackage">
            <value>com.gobha.**.dao</value>
        </property>
        <!-- 注入的名字 -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBean"/>
        <!-- 如果你想在接口使用注解 -->
        <!-- <property name="annotationClass" value="org.springframework.stereotype.Repository"/> -->
    </bean>
    <!-- 7.配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>   
    </bean>
    <!-- 8.配置哪些方法需要使用事务管理器进行管理 -->
    <tx:advice id="serviceAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="get*" read-only="true"/>
            <tx:method name="load*" read-only="true"/>
            <tx:method name="query*" read-only="true"/>
            <tx:method name="list*" read-only="true"/>
            <tx:method name="find*" read-only="true"/>
            <tx:method name="sel*" read-only="true"/>
            <tx:method name="login*" read-only="true"/>
            <tx:method name="check*" read-only="true"/>
            <tx:method name="valid*" read-only="true"/>

            <tx:method name="*"/>
        </tx:attributes>
    </tx:advice>
    <!-- 9.使用AOP告知是哪个层次下的方法被监控 -->
    <aop:config proxy-target-class="true">
        <aop:pointcut expression="execution(* com.gobha..service.*Service.*(..))" id="servicePointCut"/>
        <aop:advisor advice-ref="serviceAdvice" pointcut-ref="servicePointCut"/>
    </aop:config>
    <!-- 10.注解版本的事务 -->
    <tx:annotation-driven proxy-target-class="true"/>
```