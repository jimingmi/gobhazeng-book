# HashMap、HashTable、LinkedHahMap、TreeMap的区别

- HashMap、LinkedHahMap、TreeMap都属于Map

> **Javadoc中对Map的解释如下：**

> An objectthat maps keys to values . Amap cannot contain duplicatekeys; each key can map to at most one value.

> This interface takes the place of the Dictionary class, which was atotally abstract class rather than an interface.

> The Map interface provides three collection views, which allow amap's contents to be viewed as a set of keys, collection of values,or set of key-value mappings.

- Map主要用于存储键(Key)值(Value)对，根据键得到值，因此不允许键重复，但允许值重复。

- HashMap是一个最常用的Map，它根据键的HashCode值存储数据，根据键可以直接获取它的值，具有高效率的访问速度。

- HashMap最多只允许一条记录的键为Null，允许多条记录的值为Null，HashMap不支持线程的同步。即任一时刻可以有多个线程同时写HashMap，可能会导致数据的不一致。如果需要同步，可以用Collection的SynchronizedMap方法使HashMap具有同步的能力。

- HashTable与 HashMap类似,它继承自Dictionary类。不同的是，它不允许记录的键或者值为空，它支持线程的同步，即任一时刻只有一个线程能写HashTable,因此也导致了 Hashtable在写入时会比较慢。

- LinkedHashMap也是一个HashMap，即是它的子类，但是内部维持了一个双向链表，可以保持顺序。

- TreeMap不仅可以保持顺序，而且可以用于排序。

# HashMap的工作原理

Java中的HashMap是以键值对(KEY-VALUE)的形式存储元素的。HashMap需要一个hash函数，它使用hashCode()和equals()方法来向集合添加和检索元素。当调用put()方法的时候，HashMap会计算KEY的hash值，然后把键值对存储在集合中合适的索引上。如果KEY已经存在了，VALUE会被更新成新值。HashMap的一些重要特性是它的容量(Capacity)，负载因子(Load Factor)和扩容极限(Threshold Resizing)。
