# JVM调优

## JVM参数

|  参数值  |  参数名  |  说明  |  示例  |
|---|---|---|---|
|  -Xms  |  最小堆内存  |  设置堆容量的最小值  |  -Xms20m  |
|  -Xmx  |  最大堆内存  |  设置堆容量的最大值  |  -Xmx20m  |
|  -XX:NewSize  |  新生代初始大小  |  设置新生代的初始大小  |  -XX:NewSize2m  |
|  -XX:MaxNewSize  |  新生代的最大值  |  设置新生代的最大值  |  -XX:MaxNewSize5m  |
|  -Xmn  |  新生代的大小  |  设置新生代的大小，相当于设置了相同的-XX:NewSize和-XX:MaxNewSize  |  -Xmn5m  |
|  -XX:SurvivorRatio  |  新生代中eden空间和s0或s1空间的比值  |  公式为：eden/s0，s0和s1的大小是相同的  |  -XX:SurvivorRatio8  |
|  -XX:NewRatio  |  老年代和新生代的比值  |  公式为：老年代/新生代  |  -XX:NewRatio3  |
|  -Xoss  |  本地方法栈  |  HotSpot不区分本地方法栈与虚拟机栈，此参数对HotSpot无效  |  -Xoss128k  |
|  -Xss  |  虚拟机栈  |  设置虚拟机栈的大小  |  -Xss128k  |

## 调试参数

|  参数值  |  参数名  |  说明  |  示例  |
|---|---|---|---|
|  -verbose:gc  |  GC信息  |  输出虚拟机中GC的情况  |  -  |
|  -XX:+PrintGCDetails  |  GC详细信息  |  输出虚拟机中GC的详细情况  |  -  |
|  -XX:+HeapDumpOnOutOfMemoryError  |  内存堆转储快照  |  让JVM在出现内存溢出异常时Dump出当前的内存堆转储快照  |  -  |

## 动态对象年龄判定

长期存活的对象将进入老年代是可以通过对象年龄来控制的，但虚拟机并不是永远要求对象年龄必须达到`MaxTenuringThreshold`才能晋升到老年代，**如果在Survivor空间中相同年龄所有对象大小的总和大于Survivor空间的一半，年龄大于或等于该年龄的对象就可以直接进入老年代**，无需等到`MaxTenuringThreshold`的年龄。



## 空间分配担保

