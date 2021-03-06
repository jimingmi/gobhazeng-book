# Java内存区域

> 这里主要讨论的是使用`Serial/Serial Old`收集器下的。

![](_v_images/_1526282009_15659.png)

**线程共享：**

 - 方法区
 - 堆

**线程隔离：**

 - 虚拟机栈
 - 本地方法栈
 - 程序计数器

![](_v_images/_1526283123_28370.png)

## 程序计数器

较小的内存区域，字节码解释器通过改变这个计数器的值来选取下一条需要执行的字节码指令。

## 虚拟机栈

虚拟机栈是线程私有的，其生命周期与线程相同。

每个方法执行的时候都会创建一个栈帧用于存储局部变量表、操作数栈、动态链接、方法出口等信息。每一个方法从调直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。

一个方法的调用链可能会很长，于是当调用一个方法时，可能会有很多的方法都处于执行状态，但是对于执行引擎来讲，位于虚拟机栈顶的栈帧才是有效的，这个栈帧被称为当前栈，这个栈帧所关联的方法称为当前方法，执行引擎的所有指令都是针对当前栈帧进行操作的。

可以通过`-Xss`来改变虚拟机栈的大小。

## 本地方法栈

与虚拟机栈类似，区别在于本地方法栈执行的是Native方法。

可以通过`-Xoss`来改变本地方法栈的大小。

> HotSpot不区分本地方法栈与虚拟机栈，此参数对HotSpot无效。

## 堆

JVM中的堆目的在于存放对象实例，一般分为：

 - 新生代（Young Generation）
 - 老年代（Old Generation）

新生代又分为三个区域：

 - eden区域
 - from surviver区域
 - to surviver区域

HotSpot虚拟机默认Eden和Surviver的大小比例是8：1

Java堆一般采用分代收集算法，其中新生代一般采用复制算法、老年代一般采用标记-整理算法。

Java堆可以处于物理上不连续的内存空间，只要逻辑上连续就行。

## 方法区

方法区是各个线程共享的内存区域，用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

方法区在HotSpot虚拟机中可以分为两个主要的子区域：

 - 永久代 —— 这个区域会 存储包括类定义，结构，字段，方法（数据及代码）以及常量在内的类相关数据。它可以通过`-XX:PermSize`及`-XX:MaxPermSize`来进行调节。如果它的空间用完了，会导致`java.lang.OutOfMemoryError: PermGen space`的异常。

 - 代码缓存——这个缓存区域是用来存储编译后的代码。编译后的代码就是本地代码（硬件相关的），它是由JIT（Just In Time)编译器生成的，这个编译器是Oracle HotSpot JVM所特有的。

在JDK1.8中， HotSpot 已经没有`PermGen space`（永久代）这个区间了，取而代之是`Metaspace`（元空间）。

### 元空间

元空间位于本地堆内存(native heap)。

元空间的本质和永久代类似，都是对JVM规范中方法区的实现。不过元空间与永久代之间最大的区别在于：**元空间并不在虚拟机中，而是使用本地内存**。因此，默认情况下，元空间的大小仅受本地内存限制，但可以通过以下参数来指定元空间的大小。

 - -XX:MetaspaceSize，初始空间大小，达到该值就会触发垃圾收集进行类型卸载，同时GC会对该值进行调整：如果释放了大量的空间，就适当降低该值；如果释放了很少的空间，那么在不超过MaxMetaspaceSize时，适当提高该值。
 - -XX:MaxMetaspaceSize，最大空间，默认是没有限制的。
 - -XX:MinMetaspaceFreeRatio，在GC之后，最小的Metaspace剩余空间容量的百分比，减少为分配空间所导致的垃圾收集。
 - -XX:MaxMetaspaceFreeRatio，在GC之后，最大的Metaspace剩余空间容量的百分比，减少为释放空间所导致的垃圾收集。

**为何要使用元空间？**

 - 利用java语言的规范性：类和相关的元数据匹配的类加载器的生命周期。
 - 类及方法的信息等比较难确定其大小，因此对于永久代的大小指定比较困难，太小容易出现永久代溢出，太大则容易导致老年代溢出。
 - 永久代会为 GC 带来不必要的复杂度，并且回收效率偏低。

# 内存分配和回收策略

对象的内存分配就是在Java堆上分配，但这不完全正确。（经JIT编译进行栈上分配、启动本地线程分配缓冲在TLAB上分配）

## 垃圾收集算法



## 垃圾收集动作

![](_v_images/_1526368156_9662.png)

### 新生代GC（Minor GC）

发生在新生代的垃圾回收动作，因为Java对象大多具备朝生夕灭的特性，所以`Minor GC`非常频繁，一般回收速度也比较快。


### 老年代GC（Major/Full GC）

发生在老年代的垃圾回收动作，出现了一次`Major GC`往往会伴随至少一次的`Minor GC`（也非绝对，如Parallel Scavenge）。

`Major GC`往往会比`Minor GC`慢十倍以上。

## 对象优先在Eden分配

多数情况，对象在新生代Eden区中分配。当Eden区没有足够空间进行分配时，虚拟机将发起一次Minor GC。

```java
/**
 * 对象优先在Eden分配
 * VM Args: -Xms20m -Xmx20m -Xmn10m -XX:+PrintGCDetails -verbose:gc
 */
public static void testAllocation() {

    byte[] btye1 = new byte[3 * 1024 * 1024];
    byte[] btye2 = new byte[3 * 1024 * 1024];
    byte[] btye3 = new byte[3 * 1024 * 1024];
    byte[] btye4 = new byte[3 * 1024 * 1024];// 发生一次Minor GC

}
```

**发生Minor GC**

```java
[GC[DefNew: 7184K->474K(9216K), 0.0036759 secs] 7184K->6618K(19456K), 0.0037104 secs] [Times: user=0.02 sys=0.00, real=0.00 secs]

 def new generation   total 9216K, used 6875K [0x32800000, 0x33200000, 0x33200000)
  eden space 8192K,  78% used [0x32800000, 0x32e404d8, 0x33000000)
  from space 1024K,  46% used [0x33100000, 0x33176808, 0x33200000)
  to   space 1024K,   0% used [0x33000000, 0x33000000, 0x33100000)
 tenured generation   total 10240K, used 6144K [0x33200000, 0x33c00000, 0x33c00000)
   the space 10240K,  60% used [0x33200000, 0x33800020, 0x33800200, 0x33c00000)
 compacting perm gen  total 12288K, used 203K [0x33c00000, 0x34800000, 0x37c00000)
   the space 12288K,   1% used [0x33c00000, 0x33c32ee0, 0x33c33000, 0x34800000)
    ro space 10240K,  44% used [0x37c00000, 0x3807d3a8, 0x3807d400, 0x38600000)
    rw space 12288K,  52% used [0x38600000, 0x38c49a18, 0x38c49c00, 0x39200000)
```

**相关参数：**

|  参数值  |  参数名  |  说明  |  示例  |
|---|---|---|---|
|  -Xms  |  最小堆内存  |  设置堆容量的最小值  |  -Xms20m  |
|  -Xmx  |  最大堆内存  |  设置堆容量的最大值  |  -Xmx20m  |
|  -XX:NewSize  |  新生代初始大小  |  设置新生代的初始大小  |  -XX:NewSize2m  |
|  -XX:MaxNewSize  |  新生代的最大值  |  设置新生代的最大值  |  -XX:MaxNewSize5m  |
|  -Xmn  |  新生代的大小  |  设置新生代的大小，相当于设置了相同的-XX:NewSize和-XX:MaxNewSize  |  -Xmn5m  |

## 大对象直接进入老年代

大对象是指需要大量连续内存的Java对象，出现的大对象当新生代没有空间容纳时，虚拟机将发起一次Minor GC。之后如果仍然无法分配，大对象将直接进入老年代。

可以通过参数`-XX:PretenureSizeThreshold`来设置直接分配老年代对象的大小阈值。

```java
/**
 * 大对象直接进入老年代
 * VM Args: -Xms20m -Xmx20m -Xmn10m -XX:PretenureSizeThreshold=3145728 -XX:+PrintGCDetails -verbose:gc
 */
public static void testPretenureSizeThreshold() {

    byte[] btye = new byte[4 * 1024 * 1024];

}
```

**不设置-XX:PretenureSizeThreshold**
```java
 def new generation   total 9216K, used 5136K [0x32800000, 0x33200000, 0x33200000)
  eden space 8192K,  62% used [0x32800000, 0x32d042d0, 0x33000000)
  from space 1024K,   0% used [0x33000000, 0x33000000, 0x33100000)
  to   space 1024K,   0% used [0x33100000, 0x33100000, 0x33200000)
 tenured generation   total 10240K, used 0K [0x33200000, 0x33c00000, 0x33c00000)
   the space 10240K,   0% used [0x33200000, 0x33200000, 0x33200200, 0x33c00000)
```

**设置-XX:PretenureSizeThreshold**

```java
 def new generation   total 9216K, used 1040K [0x32800000, 0x33200000, 0x33200000)
  eden space 8192K,  12% used [0x32800000, 0x329042c0, 0x33000000)
  from space 1024K,   0% used [0x33000000, 0x33000000, 0x33100000)
  to   space 1024K,   0% used [0x33100000, 0x33100000, 0x33200000)
 tenured generation   total 10240K, used 4096K [0x33200000, 0x33c00000, 0x33c00000)
   the space 10240K,  40% used [0x33200000, 0x33600010, 0x33600200, 0x33c00000)
```

可见超过3MB的对象直接进入了老年代。

**相关参数：**

|  参数值  |  参数名  |  说明  |  示例  |
|---|---|---|---|
|  -XX:PretenureSizeThreshold  |  直接分配老年代对象的大小阈值  |  只对Serial和ParNew有效  |  -XX:PretenureSizeThreshold=3145728  |

## 长期存活的对象将进入老年代

对象优先在新生代的Eden中分配，那就存在Eden中的对象何时进入老年代的问题。JVM给meige每个对象定义了一个对象年龄（Age）计数器。如果对象在Eden出生并经过第一次Minor GC后仍然存活，并且能被Survivor容纳的话，将被移动到Survivor空间中，并且对象年龄设为1。

对象在Survivor区中每经过一次Minor GC，对象年龄就会增加1，当对象年龄达到一定数值后（默认为15），就将会被晋升到老年代中。关于晋升的对象年龄阈值，可以通过参数`-XX:MaxTenuringThreshold`设置。

```java
/**
 * 长期存活的对象将进入老年代
 * VM Args: -Xms20m -Xmx20m -Xmn10m -XX:MaxTenuringThreshold=1 -XX:+PrintGCDetails -verbose:gc
 * -XX:+PrintGCDetails -verbose:gc
 */
public static void testTenuringThreshold() {

    byte[] btye1 = new byte[10 * 1024];
    byte[] btye2 = new byte[4 * 1024 * 1024];
    byte[] btye3 = new byte[4 * 1024 * 1024];//第一次Minor GC
    btye3 = null;
    btye3 = new byte[4 * 1024 * 1024];//第二次Minor GC

}
```

**`-XX:MaxTenuringThreshold=1` 第二次Minor GC后，Survivor区的空间变为0**

```java
[GC[DefNew: 5136K->484K(9216K), 0.0029680 secs] 5136K->4580K(19456K), 0.0030075 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]
[GC[DefNew: 4664K->0K(9216K), 0.0009321 secs] 8760K->4579K(19456K), 0.0009561 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]
Heap
 def new generation   total 9216K, used 4260K [0x32800000, 0x33200000, 0x33200000)
  eden space 8192K,  52% used [0x32800000, 0x32c28fd8, 0x33000000)
  from space 1024K,   0% used [0x33000000, 0x33000088, 0x33100000)
  to   space 1024K,   0% used [0x33100000, 0x33100000, 0x33200000)
 tenured generation   total 10240K, used 4579K [0x33200000, 0x33c00000, 0x33c00000)
   the space 10240K,  44% used [0x33200000, 0x33678db8, 0x33678e00, 0x33c00000)
 compacting perm gen  total 12288K, used 203K [0x33c00000, 0x34800000, 0x37c00000)
   the space 12288K,   1% used [0x33c00000, 0x33c32fa8, 0x33c33000, 0x34800000)
    ro space 10240K,  44% used [0x37c00000, 0x3807d3a8, 0x3807d400, 0x38600000)
    rw space 12288K,  52% used [0x38600000, 0x38c49a18, 0x38c49c00, 0x39200000)
```

**`-XX:MaxTenuringThreshold=15` 第二次Minor GC后，Survivor区的空间仍然有空间占用**

```java
[GC[DefNew: 4972K->484K(9216K), 0.0028113 secs] 4972K->4580K(19456K), 0.0028464 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]
[GC[DefNew: 4744K->483K(9216K), 0.0007607 secs] 8840K->4579K(19456K), 0.0007831 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]
Heap
 def new generation   total 9216K, used 4743K [0x32800000, 0x33200000, 0x33200000)
  eden space 8192K,  52% used [0x32800000, 0x32c28fd8, 0x33000000)
  from space 1024K,  47% used [0x33000000, 0x33078e30, 0x33100000)
  to   space 1024K,   0% used [0x33100000, 0x33100000, 0x33200000)
 tenured generation   total 10240K, used 4096K [0x33200000, 0x33c00000, 0x33c00000)
   the space 10240K,  40% used [0x33200000, 0x33600010, 0x33600200, 0x33c00000)
 compacting perm gen  total 12288K, used 203K [0x33c00000, 0x34800000, 0x37c00000)
   the space 12288K,   1% used [0x33c00000, 0x33c32fa8, 0x33c33000, 0x34800000)
    ro space 10240K,  44% used [0x37c00000, 0x3807d3a8, 0x3807d400, 0x38600000)
    rw space 12288K,  52% used [0x38600000, 0x38c49a18, 0x38c49c00, 0x39200000)
```

**相关参数：**

|  参数值  |  参数名  |  说明  |  示例  |
|---|---|---|---|
|  -XX:MaxTenuringThreshold  |  新生代Survivor区中对象晋升到老年代的duixiangnian对象年龄阈值  |  不设置此值默认为15  |  -XX:MaxTenuringThreshold=1  |

## 动态对象年龄判定

## 空间分配担保

# 垃圾收集器

3.1 Serial GC算法：Serial Young GC ＋ Serial Old GC （敲黑板！敲黑板！敲黑板！实际上它是全局范围的Full GC）；

3.2 Parallel GC算法：Parallel Young GC ＋ 非并行的PS MarkSweep GC / 并行的Parallel Old GC（敲黑板！敲黑板！敲黑板！这俩实际上也是全局范围的Full GC），选PS MarkSweep GC 还是 Parallel Old GC 由参数UseParallelOldGC来控制；

3.3 CMS算法：ParNew（Young）GC + CMS（Old）GC （piggyback on ParNew的结果／老生代存活下来的object只做记录，不做compaction）＋ Full GC for CMS算法（应对核心的CMS GC某些时候的不赶趟，开销很大）；

3.4 G1 GC：Young GC + mixed GC（新生代，再加上部分老生代）＋ Full GC for G1 GC算法（应对G1 GC算法某些时候的不赶趟，开销很大）；

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
















