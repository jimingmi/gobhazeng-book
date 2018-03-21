# 对象类型转换｜toString｜比较｜包装类

## 基本数据类型的转换

- 1.自动类型转换

> 小的数据类型可以自动转换成大的数据类型。如 `long g=20; double d=12.0`

- 2.强制类型转换

> 可以把大的数据类型强制转换(casting)成小的数据类型。如 `float f=(float)12.0; int a=(int)1200L`

**对Java对象的强制类型转换称为造型**

- 规则:从子类到父类的类型转换可以自动进行

```java
public class 从子类到父类的类型转换可以自动进行 {
    public static void main(String[] args) {
        Student student = new Student();//子类
        //父类
        Person p = student;//自动类型转换
    }
}
```

- 从父类到子类的类型转换必须通过造型(强制类型转换)实现

```java
public class 从父类到子类的类型转换必须通过强制类型转换实现 {
    public static void main(String[] args) {
        Person person = new Person();//父类
        //子类
        Student s = (Student) person;//强制类型转换
    }
}
```

- 无继承关系的引用类型[类,对象,数组]间的转换是非法的

```java
public class 无继承关系的引用类型间的转换是非法的 {
    public static void main(String[] args) {
        Person p = new Person();
        //Gobha gobha= p;
        Student s = new Student();
        Gobha gobha= s;
    }
}
```

- 在造型(强制类型转换)前可以使用instanceof[比较运算符]操作符测试一个对象的类型[重点掌握]

```java
package com.gobha.app01;
public class MyDemo {
    public static void test01(Person person){
        //判断类的类型 类的类型
        System.out.print(person instanceof Student);//返回的是true
        if(person instanceof Student){
            Student s = (Student) person;//强制类型转换
        }
    }
}
```

```java
package com.gobha.app01;
public class 测试instanceof_判断类的类型 {
    public static void main(String[] args) {
        Student student = new Student();
        MyDemo.test01(student);//完成自动类型转换
    }
}
```

## Object类

- Object类是所有Java类的根父类
- 如果在类的声明中未使用extends关键字指明其父类，则默认父类为Object类
- 形参中使用的Object接收数据,那么你可以传递任意类型[请使用instanceof]类测试

```java
package com.gobha.app01;

public class DataTest {
    public static void main(String[] args) {
        Person p = new Person();
        p.say(12.0F);
    }
}
class Person{
    public void say(Object obj){//可以接收任何的数据类型,因为Object是根类
        if(obj instanceof String){
            System.out.println("您输入的为字符串:"+obj);
        }
        if(obj instanceof Person){
            System.out.println("你传递了一个对象Person:"+obj);
        }

        if(obj instanceof Long){
            System.out.println("Long:"+obj);
        }
        if(obj instanceof Float){
            System.out.println("Float:"+obj);
        }
    }
}
```

## Object类中的常用方法

- public Object() 构造方法

- public boolean equals(Object obj) 对象比较

- public int hashCode() 取得hash码

- public String toString() 对象打印时调用

**重写hashcode方法：**

```java
package com.gobha.app01;

public class Student extends Object{

    public int age;
    public int num;
    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + age;
        result = prime * result + num;
        return result;
    }
    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Student other = (Student) obj;
        if (age != other.age)
            return false;
        if (num != other.num)
            return false;
        return true;
    }
}
```

## toString方法

当你要输出对象的时候,默认情况下会调用toString()方法,一般情况下是需要你对其进行重写

- toString()方法在Object类中定义，其返回值是String类型，返回类名和它的引用地址。

> `com.gobha.app01.Student类名@10a5引用的对栈内存地址`

- 在进行String与其它类型数据的连接操作时，自动调用toString()方法

```java
Date now=new Date();
System.out.println("now="+now);  //相当于
System.out.println("now="+now.toString());
```

- 可以根据需要在用户自定义类型中重写toString()方法

```java
@Override
public String toString() {
  return "Student [age=" + age + ", num=" + num + "]";
}
```

## 包装类

**基本数据类型不是类**

- 针对八种基本定义相应的引用类型—包装类（封装类）
- 有了类的特点，就可以调用类中的方法

<table>
  <tr>
    <th bgcolor=#eeeeee>基本数据类型</th>
    <th bgcolor=#eeeeee>包装类</th>
  </tr>
  <tr>
    <td>boolean</td>
    <td>Boolean</td>
  </tr>
  <tr>
    <td>byte</td>
    <td>Byte</td>
  </tr>
  <tr>
    <td>short</td>
    <td>Short</td>
  </tr>
  <tr>
    <td>int</td>
    <td>Integer</td>
  </tr>
  <tr>
    <td>long</td>
    <td>Long</td>
  </tr>
  <tr>
    <td>char</td>
    <td>Character</td>
  </tr>
  <tr>
    <td>float</td>
    <td>Float</td>
  </tr>
  <tr>
    <td>double</td>
    <td>Double</td>
  </tr>
</table>

```java
public class 包装类测试 {
    public static void main(String[] args) {
        int i = 500;
        //Integer integer = new Integer(i);
        Integer integer=i;

        String num = "199";

        int age = Integer.parseInt(num);//重点的方法
        System.out.println(age);

        long l = 1000L;
        //String wukong = ""+l;//直接的方式
        String wukong = String.valueOf(l);//重点的方法-推荐方式

        l = Long.parseLong(wukong);//重点的方法

    }

}
```

**static**

如果想让一个类的所有实例共享数据，就用类变量

**类属性[变量]、类方法的设计思想**

- 类属性作为该类各个对象之间共享的变量。在设计类时,分析哪些类属性不因对象的不同而改变，将这些属性设置为类属性。相应的方法设置为类方法。
- 如果方法与调用者无关，则这样的方法通常被声明为类方法[工厂]，由于不需要创建对象就可以调用类方法，从而简化了方法的调用

**使用范围：**

在Java类中，可用static修饰 属性、方法、代码块、内部类[后面]
被修饰后的成员具备以下特点：

- 随着类的加载而加载
- 优先于对象存在
- 修饰的成员，被所有对象所共享
- 访问权限允许时，可不创建对象，直接被类调用

```java
package com.gobha.model;
public class Person {
    static{//只是运行了一次
        System.out.println("AA");
    }
    {
        System.out.println("BB");
    }
    public Person(){
        System.out.println("CC");
    }
}

package com.gobha.model;
public class Student extends Person {
    static{//静态代码块,类加载执行,执行一次
        System.out.println("DD");
    }
    {//代码块,在构造对象之前执行
        System.out.println("EE");
    }
    public Student(){//默认情况下调用父类的构造函数
        System.out.println("FF");
    }
}

package com.gobha.model;
public class MyTest {
    public static void main(String[] args) {
        Person person = new Student();//问控制台里面输出什么内容
    }
}
```

## Java类执行顺序（重要）

- 1. 如果父类有静态成员赋值或者静态初始化块，执行静态成员赋值和静态初始化块
- 2. 如果类有静态成员赋值或者静态初始化块，执行静态成员赋值和静态初始化块
- 3. 将类的成员赋予初值（原始类型的成员的值为规定值，例如int型为0，float型为0.0f，boolean型为false；对象类型的初始值为null）
- 4. 如果构造方法中存在this()调用（可以是其它带参数的this()调用）则执行之，执行完毕后进入第7步继续执行，如果没有this调用则进行下一步。（这个有可能存在递归调用其它的构造方法）
- 5. 执行显式的super()调用（可以是其它带参数的super()调用）或者隐式的super()调用（缺省构造方法），此步骤又进入一个父类的构造过程并一直上推至Object对象的构造。
- 6. 执行类申明中的成员赋值和初始化块。
- 7. 执行构造方法中的其它语句。

**简化版本：**

- 1. 父类的静态成员赋值和静态块
- 2. 子类的静态成员和静态块
- 3. 父类的构造方法
- 4. 父类的成员赋值和初始化块
- 5. 父类的构造方法中的其它语句
- 6. 子类的成员赋值和初始化块
- 7. 子类的构造方法中的其它语句

**单例模式**

只有一个对象,不管你声明多少次,只有一个对象

```java
package com.gobha.model;
public class Person {
    private Person(){//系统默认提供的构造器消失,禁止实例化
    }
    //共享数据
    private static Person person = new Person();
    //类方法
    public static Person getInstance(){
        return person;
    }
}

```