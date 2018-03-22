# ArrayList、LinkedList、Vector的区别

## 同步性

ArrayList、LinkedList是不同步的，而Vector是同步的。所以如果不要求线程安全的话，可以使用ArrayList或LinkedList，可以节省为同步而耗费的开销。但是在多线程的情况下，就得使用Vector。当然，也可以通过一些方法包装ArrayList、LinkedList，使它们也达到同步，但效率可能会有所降低。

## 数据增长

从内部实现机制来将ArrayList和Vector都是使用Object的数组形式来储存的。

当你向这两种类型中增加元素的时候，如果元素的数目超出了内部数组的长度它们都需要扩展内部数组的长度，Vector缺省情况下自动增长原来一倍的数组长度。

ArrayList是原来的50%，所以最后你获得的这个集合所占的空间总是比你实际需求的要大。所以要在集合中保存大量的数据那么使用Vector会比较有优势。

## 检索、插入、删除对象的效率

ArrayList和Vector中，是用下标来检索一个对象，或在集合的末尾插入、删除一个对象的时间是一样的。但是，如果在集合的其他位置增加或移除元素那么花费的时间会呈线形增长。

## 其他的一些区别

- ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表的数据结构。

- 对于随机访问Get和Set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。

- 对于新增和删除操作(add、remove)，LinkedList比较占优势，因为ArrayList要移动数据。