四种引用

JVM中的堆，一般分为：

 - 新生代（Young Generation）
 - 老年代（Old Generation）
 - 永久代（Permanent Generation）
# Java内存区域

**线程共享：**

 - 方法区
 - 堆

**线程隔离：**

 - 虚拟机栈
 - 本地方法栈
 - 程序计数器

## 程序计数器

较小的内存区域，字节码

# JVM异常

## 堆溢出

Java堆用于存储对象实例，根据可达性分析，只要保证`GC Roots`到对象间有可达路径，那么当对象数量超过最大堆容量限制后就会产生内存溢出异常。

**涉及到的参数：**

|  参数值  |  参数名  |  说明  |  示例  |
|---|---|---|---|
|  -Xms  |  最小堆内存  |  设置堆容量的最小值  |  -Xms20m  |
|  -Xmx  |  最大堆内存  |  设置堆容量的最大值  |  -Xmx20m  |

**实验：**

```java
/**
 * Java堆溢出
 * VM Args：-Xms20m -Xmx20m
 */
public static void heapOOM() {
    List<OOMObject> list = new ArrayList<>();
    while (true) {
        list.add(new OOMObject());
    }
}
```

**结果：**

```java
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
```

## 栈溢出

关于本地方法栈与虚拟机栈，JVM描述了两种异常：

 - 如果线程请求的栈深度大于虚拟机所允许的最大深度，将抛出`StackOverflowError`异常。
 - 如果JVM在扩展栈时无法申请到足够的内存空间，则抛出`OutOfMemoryError`。

需要注意的是，在多线程环境下，为每个线程的栈分配的内存越大，反而越容易产生内存溢出。

**涉及到的参数：**

|  参数值  |  参数名  |  说明  |  示例  |
|---|---|---|---|
|  -Xoss  |  本地方法栈  |  HotSpot不区分本地方法栈与虚拟机栈，此参数对HotSpot无效  |  -Xoss128k  |
|  -Xss  |  虚拟机栈  |  设置虚拟机栈的大小  |  -Xss128k  |

**实验：**

```java
/**
 * Java栈溢出
 * VM Args：-Xss128k
 */
private int stackLength = 1;

public void stackSOF() {
    stackLength++;
    System.out.println(stackLength);
    stackSOF();
}
```

**结果：**

```java
Exception in thread "main" java.lang.StackOverflowError
```

# Java的四种引用

## 强引用

只要引用存在，垃圾回收器永远不会回收

```java
Object obj = new Object();
//可直接通过obj取得对应的对象 如obj.equels(new Object());
```

而这样obj对象对后面`new Object`的一个强引用，只有当obj这个引用被释放之后，对象才会被释放掉，这是经常所用到的编码形式。

**实验：**

```java
public static void strongReferenceTest() {

    Object obj = new Object();

    System.out.println(obj);// java.lang.Object@70dea4e

    System.gc();
    System.out.println(obj);// java.lang.Object@70dea4e

    // 释放强引用
    obj = null;
    System.gc();
    System.out.println(obj);// null

}
```

## 软引用







非必须引用，内存溢出之前进行回收，可以通过以下代码实现

```java
Object obj = new Object();
SoftReference<Object> sf = new SoftReference<Object>(obj);
obj = null;
sf.get();//有时候会返回null
```

这时候sf是对obj的一个软引用，通过`sf.get()`方法可以取到这个对象，当然，当这个对象被标记为需要回收的对象时，则返回null。

软引用主要用户实现类似缓存的功能，在内存足够的情况下直接通过软引用取值，无需从繁忙的真实来源查询数据，提升速度；当内存不足时，自动删除这部分缓存数据，从真正的来源查询这些数据。

### 分析

```java
MyObject aRef = new MyObject();

SoftReference aSoftRef=new SoftReference(aRef);//此时MyObject既是SoftReference维护的软引用，又是aSoftRef所实例化的对象的强引用，所以这里MyObject是一个强可及对象

aRef = null;//这里去掉了aSoftRef的强引用，MyObject成为了一个软可及对象，垃圾收集线程会在虚拟机抛出OutOfMemoryError之前回收软可及对象，而且虚拟机会尽可能优先回收长时间闲置不用的软可及对象

MyObject anotherRef=(MyObject)aSoftRef.get(); //重新获取MyObject对象的强引用
```

**实验：**

```java
public static void softReferenceTest() {

    String value = new String("Test");

    // 使用一个软引用对象softReference去连接一个强引用对象value
    SoftReference<String> softReference = new SoftReference<String>(value);

    System.out.println(softReference.get());

    System.gc();

    // 执行GC后软引用对象依旧存在
    System.out.println(softReference.get());

}
```

## 弱引用

第二次垃圾回收时回收，可以通过如下代码实现

```java
Object obj = new Object();
WeakReference<Object> wf = new WeakReference<Object>(obj);
obj = null;
wf.get();//有时候会返回null
wf.isEnQueued();//返回是否被垃圾回收器标记为即将回收的垃圾
```

弱引用是在第二次垃圾回收时回收，短时间内通过弱引用取对应的数据，可以取到，当执行过第二次垃圾回收时，将返回null。
弱引用主要用于监控对象是否已经被垃圾回收器标记为即将回收的垃圾，可以通过弱引用的`isEnQueued()`方法返回对象是否被垃圾回收器标记。

## 虚引用

垃圾回收时回收，无法通过引用取到对象值，可以通过如下代码实现

```java
Object obj = new Object();
PhantomReference<Object> pf = new PhantomReference<Object>(obj);
obj=null;
pf.get();//永远返回null
pf.isEnQueued();//返回是否从内存中已经删除
```

虚引用是每次垃圾回收的时候都会被回收，通过虚引用的`get()`方法永远获取到的数据为null，因此也被成为幽灵引用。
虚引用主要用于检测对象是否已经从内存中删除。