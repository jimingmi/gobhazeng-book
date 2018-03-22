# Spring MVC

## 概念

SpringMVC框架,隶属于Spring框架中的一个模块

SpringMVC 可以理解为就是替换 Servlet

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251244.png)

**@Controller and @RequestMapping**

- 理解为@WebServlet(urlpatterns="/sys/login[映射路径]")

- @Controller @WebServlet用于标注其是一个控制器

- @RequestMapping用于设置映射的路径

## SpringMVC的工作原理

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251246.png)

## 环境配置步骤

- 1.需要的核心JAR包

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251250.png)

- 2.在web.xml中配置前端控制器

```xml
<servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 当服务器启动的时候就对DispatcherServlet进行实例化操作，值越小越优先 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</servlet>
```

- 3.建立核心配置文件

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251253.png)

```xml
    <!-- 1.启动SpringMVC注解 -->
    <!--
        A.需要解决下载时的字节数组的转换
        B.解决返回JSON数据格式,在IE浏览器的下载问题
        C.解决返回JSON数据格式的日期问题,查看博客的最新文章
    -->
    <mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <!-- 文件流转换器 -->
            <bean class="org.springframework.http.converter.ByteArrayHttpMessageConverter"/>
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
    <!-- 2.配置扫描,所有的扫描的包形式如下 com.cy43.**.web -->
    <context:component-scan base-package="com.gobha.**.web"/>
    <!-- 3.配置JSP请求转发的视图解析器 前缀/后缀/JSTL视图/排序 -->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/"/>
        <property name="suffix" value=".jsp"/>
        <!-- JSTL的支持 -->
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
    </bean>  
    <!-- 4.配置静态资源的排除方案,使用简单方式 -->
    <mvc:default-servlet-handler default-servlet-name="default"/>
    <!-- 5.配置文件上传的解析器,需要注意ID问题 -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 文件上传的编码格式 -->
        <property name="defaultEncoding" value="UTF-8"/>
        <!-- 配置文件上传的大小 -->
        <property name="maxUploadSize" value="20971520"/>
        <!-- 配置文件上传缓存 -->
        <property name="maxInMemorySize" value="2048"/>
        <!-- 配置文件上传的延迟操作，提高效率 -->
        <property name="resolveLazily" value="true"/>
    </bean>
    <!-- 6.暂时不配置 Session拦截器 -->

    <!-- 7.配置统一简单异常处理,目前我们匹配的映射为RuntimeException/Exception即可,后续再注解版本 -->
    <!-- <bean id="simpleExceptionCacheResolver" class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <property name="defaultErrorView" value="jsp/error03"/>
        <property name="exceptionAttribute" value="ex"/>
        <property name="exceptionMappings">
            <props>
                <prop key="ArithmeticException">jsp/error04</prop>
                <prop key="NullPointerException">jsp/error05</prop>
            </props>
        </property>
    </bean> -->
```