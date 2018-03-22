# SSM框架+MySQL

> github [项目源码](https://github.com/jimingmi/SSM_Maven)

- MyBatis：持久层框架，ORM框架，只是针对于数据库的操作

- SpringMVC：WEB层框架 ，是用来传递(客户端 <-> 服务端)和处理数据

- Spring ：IoC(DI) 和 AOP 的容器框架

- IOC 控制反转(实现方式 Java的反射机制)

- AOP 是面向切面编程 (实现方式Java的动态代理 - 使用的第三方的CGLIB动态代理)

## 使用Maven创建项目

选择创建Maven Project

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709301222.png)

勾选Create a simple project

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709301223.png)

填入Group Id、Artifact Id,packaging选择war包

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709301224.png)

右键项目—选择Properties—修改Java Build Path的JDK版本

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709301225.png)

使用本地的JDK8，之后点击apply

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709301226.png)

确认Java Compiler也为1.8版本，点击apply

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709301227.png)

进入Project Facets，取消勾选Dynamic web Module，确认Java的版本为1.8

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709301228.png)

再次进入Project Facets，选择3.0版本，勾选Dynamic web Module，点击下方Further configuration available

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709301229.png)

勾选Generate web.xml以创建XML

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709301230.png)

## 配置pow.xml

配置使用JDK1.8编译项目

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>SSM_MAVEN_demo</groupId>
    <artifactId>SSM_MAVEN_demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>war</packaging>

    <properties>
        <!-- spring版本号 -->
        <spring.version>4.3.6.RELEASE</spring.version>
        <!-- log4j日志文件管理包版本 -->
        <slf4j.version>1.6.6</slf4j.version>
        <log4j.version>1.2.12</log4j.version>
        <!-- junit版本号 -->
        <junit.version>4.10</junit.version>
        <!-- mybatis版本号 -->
        <mybatis.version>3.4.2</mybatis.version>
    </properties>

    <dependencies>
        <!-- 添加Spring依赖 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!--单元测试依赖 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- 日志文件管理包 -->
        <!-- log start -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <!-- log end -->

        <!--spring单元测试依赖 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- aspectJ支持 -->
        <dependency>
            <groupId>aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>1.5.4</version>
        </dependency>
        <dependency>
            <groupId>aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.5.4</version>
        </dependency>

        <!-- druid连接池 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.0.29</version>
        </dependency>

        <!--mybatis依赖 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>${mybatis.version}</version>
        </dependency>

        <!-- mybatis/spring包 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.0</version>
        </dependency>

        <!-- mysql驱动包 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.40</version>
        </dependency>

        <!-- Servlet-API -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
            <scope>provided</scope>
        </dependency>

        <!-- fastjson -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.4</version>
        </dependency>

        <!-- jackson -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.6.2</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.6.2</version>
        </dependency>

        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.6.2</version>
        </dependency>

        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>

        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
        </dependency>

        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.4</version>
        </dependency>

        <dependency>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
            <version>1.2</version>
        </dependency>

        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

    </dependencies>
    <build>
        <plugins>
            <!-- 使用指定JDK编译 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.0.0</version>
                <configuration>
                    <webResources>
                        <resource>
                            <directory>WebContent</directory>
                        </resource>
                    </webResources>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

右键项目，选择Maven->Update Project更新项目

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710012017.png)

## Spring + SpringMVC 整合 (放权)

SpringMVC本身就是Spring框架的一部分，所以整合相对容易，其中，两个的扫描有重复的部分。

### springMVC.xml配置

在WebContent->WEB-INF下建立spring Bean`springMVC.XML`

选择建立Spring Bean

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710012018.png)

输入文件名

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710012019.png)

选择使用的包功能，包括beans、context、mvc，注意选择版本

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710012020.png)

配置内容

```xml
    <!-- 1.启动SpringMVC注解 -->
    <mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <!-- 文件流转换器 -->
            <bean class="org.springframework.http.converter.ByteArrayHttpMessageConverter" />
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="supportedMediaTypes">
                    <list>
                        <!-- 优先使用的媒体类型，为了解决IE浏览器下，返回JSON数据的下载问题 -->
                        <value>text/html;charset=UTF-8</value>
                        <value>text/json;charset=UTF-8</value>
                        <value>application/json;charset=UTF-8</value>
                    </list>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
    <!-- 2.扫描 -->
    <context:component-scan base-package="com.gobha.**.web" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <context:include-filter type="annotation" expression="org.springframework.web.bind.annotation.ControllerAdvice"/>
    </context:component-scan>
```

### beans.xml配置

在resources目录下建立Spring Bean`beans.xml`

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710012021.png)

选择使用的包功能，包括aop、beans、context、tx，注意选择版本

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710012022.png)

配置内容

```xml
    <!-- 1.启动Spring注解 -->
    <context:annotation-config />
    <!-- 2.扫描 -->
    <context:component-scan base-package="com.gobha">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller" />
        <context:exclude-filter type="annotation" expression="org.springframework.web.bind.annotation.ControllerAdvice" />
    </context:component-scan>
```

### web.xml配置

配置内容

```xml
    <display-name>SSM_Maven</display-name>
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
```

## Spring + MyBatis  整合 (收权)

###创建JDBC配置文件`jdbc.properties`和MyBatis配置文件`mybatis-config.xml`

在resources目录下建立JDBC配置文件`jdbc.properties`

配置内容，需要修改数据库名、用户名、密码

```
# 加载MySQL驱动类
jdbc.mysql.driver=com.mysql.jdbc.Driver
# 数据库访问路径
# useUnicode=true 启动编码格式
# characterEncoding=UTF-8 设置编码格式为UTF-8
# allowMultiQueries=true 设置MySQL允许执行多条SQL语句属性
jdbc.mysql.url=jdbc:mysql://127.0.0.1:3306/数据库名?useUnicode=true&characterEncoding=UTF-8&allowMultiQueries=true
# 数据库用户名
jdbc.mysql.username=用户名
# 数据库密码
jdbc.mysql.password=密码
#-----------------------------MySQL数据配置信息----------------------------------------#

#配置初始化大小、最小、最大
jdbc.initialSize = 3
jdbc.minIdle = 5
jdbc.maxActive = 20
#配置获取连接等待超时的时间
jdbc.maxWait = 60000
#配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
jdbc.timeBetweenEvictionRunsMillis = 60000
#配置一个连接在池中最小生存的时间，单位是毫秒
jdbc.minEvictableIdleTimeMillis = 300000
#测试代码
jdbc.validationQuery = SELECT 'x'
jdbc.testWhileIdle=true
jdbc.testOnBorrow=false
jdbc.testOnReturn=false
```

### 收回连接数据源的权力

> 包括收回连接数据源的权力、收回创建SqlSessionFactory的权力、读取核心配置文件、读取映射文件的权力、事务管理器


配置beans.xml

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

### 创建log4j配置文件

在resources目录下建立log4j配置文件`log4j.properties`

配置内容

```
#可以设置级别：trace>debug>info>warn>error 
#debug：显示debug、info、error 
#info：显示info、error 
#error：只error 
# Global logging configuration
log4j.rootLogger=INFO, stdout
# 配置命名控件的位置
log4j.logger.com.gobha.dao.UserDao=TRACE
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

## 配置其他重要内容

### 完成springMVC.xml配置

配置JSP请求转发的视图解析器、配置静态资源的排除方案、配置文件上传的解析器、配置统一简单异常处理

```xml
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
    <!-- 4.配置静态资源的排除方案,使用简单方式 -->
    <mvc:default-servlet-handler default-servlet-name="default" />
    <!-- 5.配置文件上传的解析器,需要注意ID问题 -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 文件上传的编码格式 -->
        <property name="defaultEncoding" value="UTF-8" />
        <!-- 配置文件上传的大小 -->
        <property name="maxUploadSize" value="20971520" />
        <!-- 配置文件上传缓存 -->
        <property name="maxInMemorySize" value="2048" />
        <!-- 配置文件上传的延迟操作，提高效率 -->
        <property name="resolveLazily" value="true" />
    </bean>
    <!-- 6.配置统一简单异常处理,匹配的映射为RuntimeException/Exception,后续可以添加其他注解版本 -->
    <bean id="simpleExceptionCacheResolver" class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <property name="defaultErrorView" value="jsp/error" />
        <property name="exceptionAttribute" value="ex" />
        <property name="exceptionMappings">
            <props>
                <prop key="ArithmeticException">jsp/error</prop><!-- /WEB-INF/jsp/error.jsp -->
                <prop key="NullPointerException">jsp/error</prop>
            </props>
        </property>
    </bean>
```

> JSP请求转发的视图解析器即要求jsp文件需要放置在WEB-INF文件夹中保证安全，同时自动加上前后缀，简化代码

### 完成web.xml配置

配置HttpServletRequest监听器、配置Log4j的监听器、配置Spring解决缓存异常问题的监听器

```xml
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
    <welcome-file-list>
        <welcome-file>jsp/login</welcome-file>
    </welcome-file-list>
```

## 创建测试项目—模拟登陆

项目结构图

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710012147.png)

关于项目代码就不粘贴了，需要的可以去github上下载

**额外配置Session拦截器`SessionInterceptor.java`**

配置`springMVC.xml`

```xml
    <!-- 7.Session拦截器 -->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/sys/**" />
            <mvc:exclude-mapping path="/sys/login" />
            <bean class="com.gobha.interceptor.SessionInterceptor" />
        </mvc:interceptor>
    </mvc:interceptors>
```

拦截器代码

```java
import java.io.PrintWriter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

public class SessionInterceptor extends HandlerInterceptorAdapter {

	private final static String SESSION_KEY = "session_user";

	@Override
	public boolean preHandle( HttpServletRequest request , HttpServletResponse response , Object handler )
			throws Exception {
		HttpSession session = request.getSession();
		if(session.getAttribute(SESSION_KEY)==null){
			response.setContentType("text/html;charset=UTF-8");
			PrintWriter out = response.getWriter();
			out.write("<script type='text/javascript'>alert('你没有登陆！');"
					+ "window.top.location.href='"+request.getContextPath()+"/sys/login';"
					+ "</script>");
			out.flush();
			out.close();
			return false;
		}else{
			return true;
		}
	}
}
```

## 运行项目

将项目添加入本地tomcat容器

启动tomcat，浏览器访问`http://localhost:8001/SSM_MAVEN_demo/`

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710161704.png)

输入账号：123  密码：123

出现主页，项目测试完毕

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710161706.png)

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201710161705.png)