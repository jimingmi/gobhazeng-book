# Tomcat JSP

Tomcat启动的端口,默认情况下为 8080

**修改端口**

tomcat的目录下conf->server.xml的第71行修改端口的信息可以修改。

外网网址总的默认端口为80

## Eclipse中配置Tomcat步骤

- 1.配置图解

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251154.jpg)

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251155.jpg)

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251156.jpg)

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251157.jpg)

- 2.配置本地服务

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251160.jpg)

## 新建动态的WEB项目

工作空间下的动态WEB项目

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251161.jpg)

## JSP的九个内置对象

内置对象就是由Tomcat[web]服务器,预先实例化的对象并且分配一个变量,在JSP文件当中就可以直接使用该变量

- page
- pageContext
- request
- session
- appliction
- response
- out
- config
- exception

## 关于JSP的脚本

- `<% 有且只能写Java代码,不能定义方法和类 %>`

**在页面中输出值**

- out.write("可以写HTML代码和Java代码拼接");
- `<%=请千万注意其没有分号结束%>`

JSP的生命周期

理解JSP底层功能的关键就是去理解它们所遵守的生命周期。

JSP生命周期就是从创建到销毁的整个过程，类似于servlet生命周期，区别在于**JSP生命周期还包括将JSP文件编译成servlet**。

以下是JSP生命周期中所走过的几个阶段：

- 编译阶段：
servlet容器编译servlet源文件，生成servlet类

- 初始化阶段：
加载与JSP对应的servlet类，创建其实例，并调用它的初始化方法

- 执行阶段：
调用与JSP对应的servlet实例的服务方法

- 销毁阶段：
调用与JSP对应的servlet实例的销毁方法，然后销毁servlet实例

JSP生命周期的四个主要阶段和servlet生命周期非常相似，下面给出图示：

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709251205.jpg)

## JSP编译

当浏览器请求JSP页面时，JSP引擎会首先去检查是否需要编译这个文件。如果这个文件没有被编译过，或者在上次编译后被更改过，则编译这个JSP文件。

**编译的过程包括三个步骤：**

- 解析JSP文件。
- 将JSP文件转为servlet。
- 编译servlet。

## JSP初始化

容器载入JSP文件后，它会在为请求提供任何服务前调用jspInit()方法。如果您需要执行自定义的JSP初始化任务，复写jspInit()方法就行了，就像下面这样：

```java
public void jspInit(){
  // 初始化代码
}
```

一般来讲程序只初始化一次，servlet也是如此。通常情况下您可以在jspInit()方法中初始化数据库连接、打开文件和创建查询表。

## JSP执行

这一阶段描述了JSP生命周期中一切与请求相关的交互行为，直到被销毁。

当JSP网页完成初始化后，JSP引擎将会调用jspService()方法。
jspService()方法需要一个HttpServletRequest对象和一个HttpServletResponse对象作为它的参数，就像下面这样：

```java
void jspService(HttpServletRequest request,
                 HttpServletResponse response)
{
   // 服务端处理代码
}
```

jspService()方法在每个request中被调用一次并且负责产生与之相对应的response，并且它还负责产生所有7个HTTP方法的回应，比如GET、POST、DELETE等等。

## JSP清理

JSP生命周期的销毁阶段描述了当一个JSP网页从容器中被移除时所发生的一切。

jspDestroy()方法在JSP中等价于servlet中的销毁方法。当您需要执行任何清理工作时复写jspDestroy()方法，比如释放数据库连接或者关闭文件夹等等。

jspDestroy()方法的格式如下：

```java
public void jspDestroy()
{
   // 清理代码
}
```

## 获取客户端传递的数据

```html
<body>
        <h2>超级链接传递数据</h2>
        <a href="result01.jsp?id=1009">传递一个简单的数据</a>
</body>

<body>
    <%
    //获取客户端传递的数据
    String userId = request.getParameter("id"); 
    %>
    <%=userId %>
</body>
</html>
```

**传递多个参数使用 &进行连接**

```html
<body>
        <h2>超级链接传递数据</h2>
        <a href="result02.jsp?id=1009&name=tomcat">传递两个简单的数据</a>
</body>

<body>
    <%
    //获取客户端传递的数据
    String userId = request.getParameter("id");
    String userName = request.getParameter("name");
    %>
    <%=userId %>  <!-- HTML注释: -->
    <%=userName %><%-- JSP注释: out.write(userName) --%>
</body>
```

**注释**

- 1.html注释,可以才查看源码中看到
- 2.JSP注释,查看源码无法看到

## 数据的传递

### 1.客户端传递数据到服务端

**A.传递形式种类**

- 超链接传递数据
- 表单传递数据
- response.sendRedirect("路径")传递数据
- 图片传递数据
- iframe或者frame中含有src传递数据

**B.获取客户端传递的数据**

- request.getParameter("参数名称")获取对应的参数值
- request.getParameterValues("参数名")

**GET请求传递中文乱码问题**

- 第一种通过编码形式

```html
    <%
    String name = request.getParameter("name");
    //重新赋值==GET解决方案的第一种情况[查看JDK-API的java.lang.String]
    name = new String(name.getBytes("ISO8859-1"),"UTF-8");
    %>
    <h1><%=name %></h1>
```

- 第二种通过修改server.xml文件

```xml
<Connector URIEncoding="UTF-8" connectionTimeout="20000" port="8001" protocol="HTTP/1.1" redirectPort="8443"/>
```

**POST请求中文乱码问题**

```html
<h2>POST请求的中文乱码问题</h2>
<form action="result02.jsp" method="post">
  <input type="text" name="user_name" >
  <button>提交数据</button>
</form>

<%
    //解决POST请求的中文乱码问题
    request.setCharacterEncoding("UTF-8");
    String name = request.getParameter("user_name");

%>
<h1><%=name %></h1>
```

**传递复选框数据**

```html
<h2>复选框传递数据</h2>
<form action="result03.jsp">
  <input type="checkbox" name="hobbys" value="basketball">篮球
  <input type="checkbox" name="hobbys" value="football">足球
  <input type="checkbox" name="hobbys" value="pingpang">乒乓
  <button>复选框数据GET请求,为了让大家看清楚地址栏中的形式</button>
</form>

    <%
    String[] hobbys = request.getParameterValues("hobbys");
    if(hobbys!=null){
        for(String hobby : hobbys){
    %>
        <h1><%=hobby %></h1>
    <%
        }
    }
    %>
```

**传递超链接数据**

```html
<h2>超链接传递数组数据</h2>
<a href="result03.jsp?hobbys=music&hobbys=book">传递数组</a>
```

**传递文本框数据**

```html
<h2>控件名字相同就行</h2>
<form action="result03.jsp">
  <input type="text" name="hobbys" >
  <input type="text" name="hobbys" >
  <button>提交数据</button>
</form>
```

**多选列表**

```html
<h2>多选列表</h2>
<form action="result03.jsp">
  <select name="hobbys" multiple="multiple">
    <option value="music">音乐</option>
    <option value="book">读书</option>
    <option value="java">代码</option>
  </select>
  <button>提交数据</button>
</form>
```

### 2.服务端传递数据到客户端

**WEB域: 用于存取数据使用的内置对象**

- pageContext : 只在当前页面有效[一般情况下不使用]
- request:在一次请求转发当中有效,最最常用传递服务端数据的形式
- session:在一次会话当中有效,一般情况下用于存储登录用户的信息
- application: 在整个服务器中有效

**规则:**

- 能用pageContext解决就不能使用request
- 能用request解决问题就不能使用session
- 能用session解决问题就不能使用application

**作用范围:**

pageContext<request<session<application

**Session的消失方式**

每个项目下都应该有一个web.xml[部署描述符]

- 超时,配置web.xml

```xml
<session-config>
  <session-timeout>1</session-timeout>
</session-config>
```

- 手动注销

```java
<%
  session.invalidate();//session手动注销
%>
```

- 关闭浏览器

**Request的API介绍**

- request.getParameter()
- request.getParameterValues()

**获取客户端的数据形式**

- request.getAttribute("通过KEY去值") 返回的数据类型为Object
- request.setAttribute("KEY",Object)
- request.getRequestDispatcher("/从项目发布路径开始查找").forward(request,response)

## 创建JSP模版

```html
<%@ page language="java" contentType="text/html; charset=${encoding}"
    pageEncoding="${encoding}"%>
<%
    String path = request.getContextPath();
    String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>
<!DOCTYPE html>
<html>
    <head>
        <base href="<%=basePath%>">
        <meta charset="${encoding}">
        <title>HTML5模版</title>
    </head>
    <body>

    </body>
</html>
```