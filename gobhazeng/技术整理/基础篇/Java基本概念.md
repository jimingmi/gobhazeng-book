# Java语言特点

**1. 面向对象(OOP)**

面向对象由类(class)和对象组成

封装、继承、多态

**2. 安全性（健壮性）**

编译为字节码文件

**3. 跨平台**

一次编写，处处运行（需要借助JVM实现）

# JDK、JRE、JVM三者之间的关系

**JDK > JRE > JVM**

**JDK**

- Java SE
- JAVA SE
- JAVA EE
- JAVA ME
- JAVA Card

> JDK是开发的时候使用，因为里面含有开发工具，java.exejavac.exe

**JRE**

Java 运行时的环境，只能运行的class文件

**JVM**

针对于不同的操作系统，class文件进行相应的解析

# Java的核心机制

**JVM、GC**

# 标识符与关键字

## 标识符

只要是可以自己命名的地方，都是标识符

- 组成: 数字,字母,下划线和美元符号$
- 不能使用数字开头
- 不能还有特殊字符串,空格
- 不能使用关键字和保留字
- 严格区分大小写
- 见名知意
- 潜规则: 尽量不要使用美元符号开头
- 潜规则: int aA标识符前两个字母尽量不要使用一小一大

## 关键字

都是小写,已经被Java语言使用

## 保留字

未来可能使用

# 变量

## 字面值

一眼就能看出来的值的类型就是字面 

`System.out.println(100);`

`System.out.println("悟空");`

字面值的数据是存储在内存当中

## 变量

用于存储数据

# 数据类型

## 基本数据类型

**整数类型**

- 字节型 byte 8Bit 取值范围 -128到127

- 短整型 short 16Bit

- 整型 int 32Bit 字面值默认情况下为int类型

- 长整型 long 64Bit

 - 如何声明长整型数据 `long number=100L;long num1=1000l;`

**浮点类型**

- 双精度浮点型 double 64Bit 字面值默认情况下为double 类型

- 单精度浮点型 float 32Bit `float number=100F;float num1=1000f;`

**字符类型**

- char 使用单引号 16Bit

**布尔类型**

- boolean 只有true和false 8Bit

## 引用数据类型

- 对象（字符串String）

- 数组

- 类

# Java基本数据类型自动转换的规则

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709240014.png)

# Java强制转换的规则

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709240016.png)

# 成员变量与局部变量

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/201709240022.png)