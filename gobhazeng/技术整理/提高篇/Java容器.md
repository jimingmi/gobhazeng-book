# 1. ArrayList

## 1.1 概述

ArrayList是实现List接口的动态数组，所谓动态就是它的大小是可变的。实现了所有可选列表操作，并允许包括 null 在内的所有元素。

每个ArrayList实例都有一个容量，该容量是指用来存储列表元素的数组的大小。默认初始容量为10。随着ArrayList中元素的增加，它的容量也会不断的自动增长。在每次添加新的元素时，ArrayList都会检查是否需要进行扩容操作，扩容操作带来数据向新数组的重新拷贝，所以如果我们知道具体业务数据量，在构造ArrayList时可以给ArrayList指定一个初始容量，这样就会减少扩容时数据的拷贝问题。当然在添加大量元素前，应用程序也可以使用ensureCapacity操作来增加ArrayList实例的容量，这可以减少递增式再分配的数量。

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

ArrayList实现并不是同步的，如果需要同步，可以使用：

```java
List list = Collections.synchronizedList(new ArrayList(...));
```

### 1.1.1 构造函数

 ArrayList提供了三个构造函数：

```java
/**
 * 构造一个初始容量为 10 的空列表
 */
public ArrayList() {
    this(10);
}

/**
 * 构造一个具有指定初始容量的空列表。
 */
public ArrayList(int initialCapacity) {
    super();
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal Capacity: "
                + initialCapacity);
    this.elementData = new Object[initialCapacity];
}

/**
 *  构造一个包含指定 collection 的元素的列表，这些元素是按照该 collection 的迭代器返回它们的顺序排列的。
 */
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    size = elementData.length;
    // c.toArray might (incorrectly) not return Object[] (see 6260652)
    if (elementData.getClass() != Object[].class)
        elementData = Arrays.copyOf(elementData, size, Object[].class);
}
```

### 1.1.2 成员变量

ArrayList的底层容器数组elementData

```java
transient Object[] elementData;
```

> **transient：**java关键字，为变量修饰符，如果用transient声明一个实例变量，当对象存储时，它的值不需要维持。Java的serialization提供了一种持久化对象实例的机制。当持久化对象时，可能有一个特殊的对象数据成员，我们不想用serialization机制来保存它。为了在一个特定对象的一个域上关闭serialization，可以在这个域前加上关键字transient。当一个对象被序列化的时候，transient型变量的值不包括在序列化的表示中，然而非transient型的变量是被包括进去的。

## 1.2 源码分析

### 1.2.1 增加

ArrayList提供了add(E e)、add(int index, E element)、addAll(Collection<? extends E> c)、addAll(int index, Collection<? extends E> c)、set(int index, E element)这个五个方法来实现ArrayList增加。

**add(E e)**：将指定元素添加到此列表的末尾。

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);//确认容器大小，如果操作容量则扩容操作
    elementData[size++] = e;//将e元素添加至末尾
    return true;
}
```

**add(int index, E element)**：将指定的元素插入此列表中的指定位置。

```java
public void add(int index, E element) {
    rangeCheckForAdd(index);//判断索引位置是否正确

    ensureCapacityInternal(size + 1);//确认容器大小，如果操作容量则扩容操作
    /*
     * 对源数组进行复制处理（位移），从index + 1到size-index。
     * 主要目的就是空出index位置供数据插入，
     * 即向右移动当前位于该位置的元素以及所有后续元素。
     */
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    elementData[index] = element;//在指定位置赋值
    size++;
}
```

**addAll(Collection<? extends E> c)**：按照指定 collection 的迭代器所返回的元素顺序，将该 collection 中的所有元素添加到此列表的尾部。

```java
public boolean addAll(Collection<? extends E> c) {
    // 将集合C转换成数组
    Object[] a = c.toArray();
    int numNew = a.length;
    // 扩容处理，大小为size + numNew
    ensureCapacity(size + numNew); // Increments modCount
    System.arraycopy(a, 0, elementData, size, numNew);
    size += numNew;
    return numNew != 0;
}
```

**addAll(int index, Collection<? extends E> c)**：从指定的位置开始，将指定 collection 中的所有元素插入到此列表中。

```java
public boolean addAll(int index, Collection<? extends E> c) {
    //判断位置是否正确
    if (index > size || index < 0)
        throw new IndexOutOfBoundsException("Index: " + index + ", Size: "
                + size);
    //转换成数组
    Object[] a = c.toArray();
    int numNew = a.length;
    //ArrayList容器扩容处理
    ensureCapacity(size + numNew); // Increments modCount
    //ArrayList容器数组向右移动的位置
    int numMoved = size - index;
    //如果移动位置大于0，则将ArrayList容器的数据向右移动numMoved个位置，确保增加的数据能够增加
    if (numMoved > 0)
        System.arraycopy(elementData, index, elementData, index + numNew,
                numMoved);
    //添加数组
    System.arraycopy(a, 0, elementData, index, numNew);
    //容器容量变大
    size += numNew;
    return numNew != 0;
}
```

**set(int index, E element)**：用指定的元素替代此列表中指定位置上的元素。

```java
public E set(int index, E element) {
    //检测插入的位置是否越界
    RangeCheck(index);

    E oldValue = (E) elementData[index];
    //替代
    elementData[index] = element;
    return oldValue;
}
```

### 1.2.2 删除

ArrayList提供了remove(int index)、remove(Object o)、removeRange(int fromIndex, int toIndex)、removeAll()四个方法进行元素的删除。

**remove(int index)**：移除此列表中指定位置上的元素。

```java
public E remove(int index) {
    //位置验证
    RangeCheck(index);

    modCount++;
    //需要删除的元素
    E oldValue = (E) elementData[index];
    //向左移的位数
    int numMoved = size - index - 1;
    //若需要移动，则想左移动numMoved位
    if (numMoved > 0)
        System.arraycopy(elementData, index + 1, elementData, index,numMoved);
    //置空最后一个元素
    elementData[--size] = null; // Let gc do its work

    return oldValue;
}
```

**remove(Object o)**：移除此列表中首次出现的指定元素（如果存在）。

```java
public boolean remove(Object o) {
    //因为ArrayList中允许存在null，所以需要进行null判断
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                //移除这个位置的元素
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}
```

**fastRemove(int index)**：方法用于移除指定位置的元素。

```java
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,numMoved);
    elementData[--size] = null; // Let gc do its work
}
```

**removeRange(int fromIndex, int toIndex)**：移除列表中索引在 fromIndex（包括）和 toIndex（不包括）之间的所有元素。

```java
protected void removeRange(int fromIndex, int toIndex) {
    modCount++;
    int numMoved = size - toIndex;
    System.arraycopy(elementData, toIndex, elementData, fromIndex,numMoved);

    // Let gc do its work
    int newSize = size - (toIndex - fromIndex);
    while (size != newSize)
        elementData[--size] = null;
}
```

**removeAll(Collection<?> c)**：将指定 collection 中的所有元素从此列表中删除。

```java
public boolean removeAll(Collection<?> c) {
    Objects.requireNonNull(c);
    return batchRemove(c, false);
}

private boolean batchRemove(Collection<?> c, boolean complement) {
    final Object[] elementData = this.elementData;
    int r = 0, w = 0;
    boolean modified = false;
    try {
        for (; r < size; r++)
            if (c.contains(elementData[r]) == complement)
                elementData[w++] = elementData[r];
    } finally {
        // Preserve behavioral compatibility with AbstractCollection,
        // even if c.contains() throws.
        if (r != size) {
            System.arraycopy(elementData, r,
                             elementData, w,
                             size - r);
            w += size - r;
        }
        if (w != size) {
            // clear to let GC do its work
            for (int i = w; i < size; i++)
                elementData[i] = null;
            modCount += size - w;
            size = w;
            modified = true;
        }
    }
    return modified;
}
```

### 1.2.3 查找

ArrayList提供了get(int index)用读取ArrayList中的元素。由于ArrayList是动态数组，所以我们完全可以根据下标来获取ArrayList中的元素，而且速度还比较快，故ArrayList长于随机访问。

```java
public E get(int index) {
    rangeCheck(index);

    return elementData(index);
}

private void rangeCheck(int index) {
    if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}

E elementData(int index) {
    return (E) elementData[index];
}
```

### 1.2.4 扩容

ArrayList源码中经常出现方法ensureCapacity()，该方法就是ArrayList的扩容方法。在前面就提过ArrayList每次新增元素时都会需要进行容量检测判断，若新增元素后元素的个数会超过ArrayList的容量，就会进行扩容操作来满足新增元素的需求。所以当我们清楚知道业务数据量或者需要插入大量元素前，我可以使用ensureCapacity来手动增加ArrayList实例的容量，以减少递增式再分配的数量。

```java
public void ensureCapacity(int minCapacity) {
    int minExpand = (elementData != EMPTY_ELEMENTDATA)
        // any size if real element table
        ? 0
        // larger than default for empty table. It's already supposed to be
        // at default size.
        : DEFAULT_CAPACITY;

    if (minCapacity > minExpand) {
        ensureExplicitCapacity(minCapacity);
    }
}
```

# 2. LinkedList

## 2.1 概述

LinkedList与ArrayList一样实现List接口，只是ArrayList是List接口的大小可变数组的实现，LinkedList是List接口链表的实现。基于链表实现的方式使得LinkedList在插入和删除时更优于ArrayList，而随机访问则比ArrayList逊色些。

LinkedList实现所有可选的列表操作，并允许所有的元素包括null。

LinkedList继承AbstractSequentialList，实现List、Deque、Cloneable、Serializable。其中AbstractSequentialList提供了 List 接口的骨干实现，从而最大限度地减少了实现受“连续访问”数据存储（如链接列表）支持的此接口所需的工作,从而以减少实现List接口的复杂度。Deque一个线性 collection，支持在两端插入和移除元素，定义了双端队列的操作。

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```

### 2.1.1 数据结构

![](_v_images/_1553826117_4370.png)

说明：LinkedList底层使用的双向链表结构，有一个头结点和一个尾结点，双向链表意味着我们可以从头开始正向遍历，或者是从尾开始逆向遍历，并且可以针对头部和尾部进行相应的操作。

### 2.1.1 构造函数

```java
/**
 *  构造一个空列表。
 */
public LinkedList() {
}

/**
 *  构造一个包含指定 collection 中的元素的列表，这些元素按其 collection 的迭代器返回的顺序排列。
 */
public LinkedList(Collection<? extends E> c) {
    // 调用无参构造函数
    this();
    // 添加集合中所有的元素
    addAll(c);
}
```

### 2.1.2 成员变量

LinkedList中提供了三个基本属性size、first、last。

 - **size**：LinkedList的大小
 - **first**：表示链表的头结点
 - **last**：表示链表的尾节点
 - **Node**：为节点对象

```java
// 实际元素个数
transient int size = 0;
// 头结点
transient Node<E> first;
// 尾结点
transient Node<E> last;
```

### 2.1.3 内部类

内部类Node就是实际的结点，用于存放实际元素的地方。

```java
private static class Node<E> {
    E item; // 数据域
    Node<E> next; // 后继
    Node<E> prev; // 前驱

    // 构造函数，赋值前驱后继
    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

## 2.2 源码分析

### 2.2.1 增加

**add(E e)**：向LinkedList中添加一个元素，并且添加到链表尾部。

```java
public boolean add(E e) {
    linkLast(e);
    return true;
}

void linkLast(E e) {
    // 保存尾结点，l为final类型，不可更改
    final Node<E> l = last;
    // 新生成结点的前驱为l,后继为null
    final Node<E> newNode = new Node<>(l, e, null);
    // 重新赋值尾结点
    last = newNode;
    if (l == null) // 尾结点为空
        first = newNode; // 赋值头结点
    else // 尾结点不为空
        l.next = newNode; // 尾结点的后继为新生成的结点
    // 大小加1
    size++;
    // 结构性修改加1
    modCount++;
}
```

**addAll(Collection<? extends E> c)**：添加一个集合

参数中的index表示在索引下标为index的结点（实际上是第index + 1个结点）的前面插入。

```java
public boolean addAll(Collection<? extends E> c) {
    return addAll(size, c);
}

public boolean addAll(int index, Collection<? extends E> c) {
    // 检查插入的的位置是否合法
    checkPositionIndex(index);
    // 将集合转化为数组
    Object[] a = c.toArray();
    // 保存集合大小
    int numNew = a.length;
    if (numNew == 0) // 集合为空，直接返回
        return false;

    Node<E> pred, succ; // 前驱，后继
    if (index == size) { // 如果插入位置为链表末尾，则后继为null，前驱为尾结点
        succ = null;
        pred = last;
    } else { // 插入位置为其他某个位置
        succ = node(index); // 寻找到该结点
        pred = succ.prev; // 保存该结点的前驱
    }

    for (Object o : a) { // 遍历数组
        @SuppressWarnings("unchecked") E e = (E) o; // 向下转型
        // 生成新结点
        Node<E> newNode = new Node<>(pred, e, null);
        if (pred == null) // 表示在第一个元素之前插入(索引为0的结点)
            first = newNode;
        else
            pred.next = newNode;
        pred = newNode;
    }

    if (succ == null) { // 表示在最后一个元素之后插入
        last = pred;
    } else {
        pred.next = succ;
        succ.prev = pred;
    }
    // 修改实际元素个数
    size += numNew;
    // 结构性修改加1
    modCount++;
    return true;
}
```

其中，addAll函数和get函数会调用到node函数，此函数是根据索引下标找到该结点并返回，在根据索引查找结点时，会有一个小优化，结点在前半段则从头开始遍历，在后半段则从尾开始遍历，这样就保证了只需要遍历最多一半结点就可以找到指定索引的结点。

```java
Node<E> node(int index) {
    // 判断插入的位置在链表前半段或者是后半段
    if (index < (size >> 1)) { // 插入位置在前半段
        Node<E> x = first; 
        for (int i = 0; i < index; i++) // 从头结点开始正向遍历
            x = x.next;
        return x; // 返回该结点
    } else { // 插入位置在后半段
        Node<E> x = last; 
        for (int i = size - 1; i > index; i--) // 从尾结点开始反向遍历
            x = x.prev;
        return x; // 返回该结点
    }
}
```

### 2.2.2 移除

```java
public E remove(int index) {
    checkElementIndex(index);
    return unlink(node(index));
}

private void checkElementIndex(int index) {
    if (!isElementIndex(index))
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}

E unlink(Node<E> x) {
    // 保存结点的元素
    final E element = x.item;
    // 保存x的后继
    final Node<E> next = x.next;
    // 保存x的前驱
    final Node<E> prev = x.prev;

    if (prev == null) { // 前驱为空，表示删除的结点为头结点
        first = next; // 重新赋值头结点
    } else { // 删除的结点不为头结点
        prev.next = next; // 赋值前驱结点的后继
        x.prev = null; // 结点的前驱为空，切断结点的前驱指针
    }

    if (next == null) { // 后继为空，表示删除的结点为尾结点
        last = prev; // 重新赋值尾结点
    } else { // 删除的结点不为尾结点
        next.prev = prev; // 赋值后继结点的前驱
        x.next = null; // 结点的后继为空，切断结点的后继指针
    }

    x.item = null; // 结点元素赋值为空
    // 减少元素实际个数
    size--;
    // 结构性修改加1
    modCount++;
    // 返回结点的旧元素
    return element;
}
```

# 3. Vector

## 3.1 概述

Vector可以实现可增长的对象数组。与数组一样，它包含可以使用整数索引进行访问的组件。不过，Vector的大小是可以增加或者减小的，以便适应创建Vector后进行添加或者删除操作。

Vector实现List接口，继承AbstractList类，所以我们可以将其看做队列，支持相关的添加、删除、修改、遍历等功能。

Vector实现RandmoAccess接口，即提供了随机访问功能，提供提供快速访问功能。在Vector我们可以直接访问元素。

Vector 实现了Cloneable接口，支持clone()方法，可以被克隆。

```java
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

### 3.1.1 构造函数

```java
/**
 * 构造一个空向量，使其内部数据数组的大小为 10，其标准容量增量为零。
 */
public Vector() {
    this(10);
}

/**
 * 构造一个包含指定 collection 中的元素的向量，这些元素按其 collection 的迭代器返回元素的顺序排列。
 */
public Vector(Collection<? extends E> c) {
    elementData = c.toArray();
    elementCount = elementData.length;
    // c.toArray might (incorrectly) not return Object[] (see 6260652)
    if (elementData.getClass() != Object[].class)
        elementData = Arrays.copyOf(elementData, elementCount,
                Object[].class);
}

/**
 * 使用指定的初始容量和等于零的容量增量构造一个空向量。
 */
public Vector(int initialCapacity) {
    this(initialCapacity, 0);
}

/**
 *  使用指定的初始容量和容量增量构造一个空的向量。
 */
public Vector(int initialCapacity, int capacityIncrement) {
    super();
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    this.elementData = new Object[initialCapacity];
    this.capacityIncrement = capacityIncrement;
}
```

### 3.1.2 成员变量

在成员变量方面，Vector提供了elementData , elementCount， capacityIncrement三个成员变量。其中：

 - **elementData** ："Object[]类型的数组"，它保存了Vector中的元素。按照Vector的设计elementData为一个动态数组，可以随着元素的增加而动态的增长，其具体的增加方式后面提到（ensureCapacity方法）。如果在初始化Vector时没有指定容器大小，则使用默认大小为10.

 - **elementCount**：Vector 对象中的有效组件数。

 - **capacityIncrement**：向量的大小大于其容量时，容量自动增加的量。如果在创建Vector时，指定了capacityIncrement的大小；则，每次当Vector中动态数组容量增加时>，增加的大小都是capacityIncrement。如果容量的增量小于等于零，则每次需要增大容量时，向量的容量将增大一倍。

```java

protected Object[] elementData;

protected int elementCount;

protected int capacityIncrement;

```

> 由于Vector使用synchronized，因此Vector是线程安全的。

## 3.2 源码分析

### 3.2.1 增加

**add(E e)**：将指定元素添加到此列表的末尾。

```java
public synchronized boolean add(E e) {
    modCount++;
    ensureCapacityHelper(elementCount + 1);//确认容器大小，如果操作容量则扩容操作
    elementData[elementCount++] = e;//将e元素添加至末尾
    return true;
}
```

### 3.3.2 移除

**remove(Object o)**：从Vector容器中移除指定元素E

```java
public boolean remove(Object o) {
    return removeElement(o);
}

public synchronized boolean removeElement(Object obj) {
    modCount++;
    int i = indexOf(obj);//计算obj在Vector容器中位置
    if (i >= 0) {
        removeElementAt(i);//移除
        return true;
    }
    return false;
}

public synchronized void removeElementAt(int index) {
    modCount++;//修改次数+1
    if (index >= elementCount) {//删除位置大于容器有效大小
        throw new ArrayIndexOutOfBoundsException(index + " >= " +
                                                 elementCount);
    }
    else if (index < 0) {//删除位置小于 < 0
        throw new ArrayIndexOutOfBoundsException(index);
    }
    int j = elementCount - index - 1;
    if (j > 0) {
        //从指定源数组中复制一个数组，复制从指定的位置开始，到目标数组的指定位置结束。
        //也就是数组元素从j位置往前移
        System.arraycopy(elementData, index + 1, elementData, index, j);
    }
    elementCount--;//容器中有效组件个数 - 1
    elementData[elementCount] = null; //将向量的末尾位置设置为null 触发gc
}
```

> 因为Vector底层是使用数组实现的，所以它的操作都是对数组进行操作，只不过其是可以随着元素的增加而动态的改变容量大小，其实现方法是是使用Arrays.copyOf方法将旧数据拷贝到一个新的大容量数组中。

## 3.4 遍历

**随机访问：**

因为Vector实现了RandmoAccess接口，可以通过下标来进行随机访问。

```java
for(int i = 0 ; i < vec.size() ; i++){
    value = vec.get(i);
}
```

**迭代器：**

```java
Iterator it = vec.iterator();
while(it.hasNext()){
    value = it.next();
    //do something
}
```

**for循环：**

```java
for(Integer value:vec){
    //do something
}
```

**Enumeration循环：**

```java
Vector vec = new Vector<>();
Enumeration enu = vec.elements();
while (enu.hasMoreElements()) {
    value = (Integer)enu.nextElement();
}
```

# 4. HashMap

**写在前面：**

快速过一遍数据结构！

 - **数组**：采用一段连续的存储单元来存储数据。对于指定下标的查找，时间复杂度为O(1)；通过给定值进行查找，需要遍历数组，逐一比对给定关键字和数组元素，时间复杂度为O(n)，当然，对于有序数组，则可采用二分查找，插值查找，斐波那契查找等方式，可将查找复杂度提高为O(logn)；对于一般的插入删除操作，涉及到数组元素的移动，其平均复杂度也为O(n)。对应到集合实现，代表就是ArrayList。
 - **线性链表**：对于链表的新增，删除等操作（在找到指定操作位置后），仅需处理结点间的引用即可，时间复杂度为O(1)，而查找操作需要遍历链表逐一进行比对，复杂度为O(n)。对应的集合类是LinkedList。
 - **二叉树**：对一棵相对平衡的有序二叉树，对其进行插入，查找，删除等操作，平均复杂度均为O(logn)。对应的集合类有TreeSet和TreeMap。
 - **哈希表**：相比上述几种数据结构，在哈希表中进行添加，删除，查找等操作，性能十分之高，不考虑哈希冲突的情况下，仅需一次定位即可完成，时间复杂度为O(1)。对应的集合类就是HashMap。

## 3.1 概述