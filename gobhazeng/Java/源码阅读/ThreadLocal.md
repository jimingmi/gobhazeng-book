# 概念

ThreadLocal即线程局部变量。

在并发编程的时候，成员变量如果不做任何处理其实是线程不安全的，各个线程都在操作同一个变量，存在线程安全问题。即使使用`volatile`修饰局部变量也是不能保证线程安全的，`volatile`不能保证原子性。

那么在有一种情况之下，我们需要满足这样一个条件：变量是同一个，但是每个线程都使用同一个初始值，也就是使用同一个变量的一个新的副本。这种情况之下ThreadLocal就非常适用，比如说`DAO`的数据库连接，由于`DAO`是单例的（这里是单纯建立一个连接），那么他的属性`Connection`就不是一个线程安全的变量。而我们每个线程都需要使用他，并且各自使用各自的。这种情况，ThreadLocal就比较好的解决了这个问题。

```java
public class ThreadLoaclTest {

	private ThreadLoaclTest() {
	}

	private static final ThreadLocal<Connection> conn = new ThreadLocal<>();

	public static Connection getConn() {
		Connection con = conn.get();
		if (con == null) {
			try {
				Class.forName("com.mysql.jdbc.Driver");
				con = DriverManager.getConnection("url", "userName", "password");
				conn.set(con);
			} catch (ClassNotFoundException | SQLException e) {
				e.printStackTrace();
			}
		}
		return con;
	}

}
```

# 源码分析

从`Thread`的源码中可以发现这段代码。

```java
/* ThreadLocal values pertaining to this thread. This map is maintained
 * by the ThreadLocal class. */
ThreadLocal.ThreadLocalMap threadLocals = null;
```

可见，`Thread`维护了一个`ThreadLocalMap`这个`ThreadLocal`的内部类，用来存放若干个`ThreadLocal`。

当调用`ThreadLocal.get()`方法时,

```java
/**
 * Returns the value in the current thread's copy of this
 * thread-local variable.  If the variable has no value for the
 * current thread, it is first initialized to the value returned
 * by an invocation of the {@link #initialValue} method.
 *
 * @return the current thread's value of this thread-local
 */
public T get() {
    //获取当前线程
    Thread t = Thread.currentThread();
    //获取线程Thread中的ThreadLocalMap
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        //取出ThreadLocal的value
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    //其他情况进行初始化，即将initialValue的值set到ThreadLocal中
    return setInitialValue();
}

/**
     * Sets the current thread's copy of this thread-local variable
     * to the specified value.  Most subclasses will have no need to
     * override this method, relying solely on the {@link #initialValue}
     * method to set the values of thread-locals.
     *
     * @param value the value to be stored in the current thread's copy of
     *        this thread-local.
     */
    public void set(T value) {
        //获取当前线程
        Thread t = Thread.currentThread();
        //获取线程Thread中的ThreadLocalMap
        ThreadLocalMap map = getMap(t);
        //如果不为空，则设置值
        if (map != null)
            map.set(this, value);
        else
        //为空则初始化Map
            createMap(t, value);
    }
```

由此可见，每次调用get方法的时候，由于每个当前线程中都有一个`ThreadLocal`。所以每次获取或者设置都是对当前线程的`ThreadLocal`进行的操作，是与其他线程分开的。