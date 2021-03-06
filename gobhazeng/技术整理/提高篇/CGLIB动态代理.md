# CGLIB动态代理

CGLIB实现的动态代理，完全不受代理类必须实现接口的限制，而且CGLIB底层采用ASM字节码生成框架，使用字节码技术生成代理类，比使用Java反射效率要高。唯一需要注意的是，CGLIB不能对声明为final的方法进行代理，因为CGLIB原理是动态生成被代理类的子类。

在使用Spring的AOP时，关于动态代理往往我们会使用CGLIB而不是JDK的实现，这里探究一下CGLIB的使用与其内部实现。

## 一个简单运用

**需要引入Jar包**

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.2.6</version>
</dependency>
```

**cglib代理入口**

```java
import net.sf.cglib.proxy.Enhancer;

/**
 * @Description: cglib代理
 * @Author: Gobhazeng
 * @Date: 0:13 2018/3/24
 */
public class Client {

    public static void main(String[] args) {
        //cglib基础类
        Enhancer enhancer = new Enhancer();
        //继承被代理类
        enhancer.setSuperclass(HelloServiceImpl.class);
        //设置回调
        enhancer.setCallback(new HelloMethodInterceptor());
        //生成代理类对象
        HelloServiceImpl helloService = (HelloServiceImpl) enhancer.create();
        //执行方法 这个方法会被回调中实现的方法拦截器进行拦截
        helloService.sayHello();
    }
}
```

**自定义方法拦截器**

```java
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

/**
 * @Description: 方法拦截器
 * @Author: Gobhazeng
 * @Date: 0:10 2018/3/24
 */
public class HelloMethodInterceptor implements MethodInterceptor {

    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        //在方法执行前执行
        System.out.println("Before:" + method.getName());
        //执行拦截的方法
        Object object = proxy.invokeSuper(obj, args);
        //在方法拦截后执行
        System.out.println("After:" + method.getName());
        return object;
    }
}
```

**代理类**

```java
/**
 * @Description: 代理类
 * @Author: Gobhazeng
 * @Date: 0:09 2018/3/24
 */
public class HelloServiceImpl {

    public void sayHello(){
        System.out.println("Hello EakonZhao");
    }
}
```

JDK实现的动态代理要求代理类必须实现接口，具有一定的局限性。

而CGLIB并不需要这样，只需要继承代理类，并在代理类中对代理方法进行强化处理(前置处理、后置处理等)。

CGLIB底层，是使用ASM字节码生成框架来实现的。

## 回调类型

除了功能强大的`MethodInterceptor`，CGLIB还有其他的特殊回调类型。

 - `FixedValue`：在强制一个特定方法返回固定值，在特定场景下非常有用且性能高。
 - `NoOp`：它直接透传到父类的方法实现。
 - `LazyLoader`：在被代理对象需要懒加载场景下非常有用，如果被代理对象加载完成，那么在以后的代理调用时会重复使用。
 - `Dispatcher`：与`LazyLoader`差不多，但每次调用代理方法时都会调用`loadObject()`方法来加载被代理对象。
 - `ProxyRefDispatcher`：与`Dispatcher`相同，但它的`loadObject()`方法支持传入代理对象。