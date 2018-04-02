# 集合的比较

## HashMap、HashTable、LinkedHahMap、TreeMap的区别

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

### HashMap的工作原理

Java中的HashMap是以键值对(KEY-VALUE)的形式存储元素的。HashMap需要一个hash函数，它使用hashCode()和equals()方法来向集合添加和检索元素。当调用put()方法的时候，HashMap会计算KEY的hash值，然后把键值对存储在集合中合适的索引上。如果KEY已经存在了，VALUE会被更新成新值。HashMap的一些重要特性是它的容量(Capacity)，负载因子(Load Factor)和扩容极限(Threshold Resizing)。

## ArrayList、LinkedList、Vector的区别

### 同步性

ArrayList、LinkedList是不同步的，而Vector是同步的。所以如果不要求线程安全的话，可以使用ArrayList或LinkedList，可以节省为同步而耗费的开销。但是在多线程的情况下，就得使用Vector。当然，也可以通过一些方法包装ArrayList、LinkedList，使它们也达到同步，但效率可能会有所降低。

### 数据增长

从内部实现机制来将ArrayList和Vector都是使用Object的数组形式来储存的。

当你向这两种类型中增加元素的时候，如果元素的数目超出了内部数组的长度它们都需要扩展内部数组的长度，Vector缺省情况下自动增长原来一倍的数组长度。

ArrayList是原来的50%，所以最后你获得的这个集合所占的空间总是比你实际需求的要大。所以要在集合中保存大量的数据那么使用Vector会比较有优势。

### 检索、插入、删除对象的效率

ArrayList和Vector中，是用下标来检索一个对象，或在集合的末尾插入、删除一个对象的时间是一样的。但是，如果在集合的其他位置增加或移除元素那么花费的时间会呈线形增长。

### 其他的一些区别

- ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表的数据结构。

- 对于随机访问Get和Set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。

- 对于新增和删除操作(add、remove)，LinkedList比较占优势，因为ArrayList要移动数据。
