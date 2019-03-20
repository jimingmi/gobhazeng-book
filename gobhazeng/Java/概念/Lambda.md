#  Lambda用于匿名类

对于只有一个方法的接口，一般称之为函数式接口，如`Runnable`接口。

这里Lambda可以使用`()->{}`来代替匿名函数。

```java
/**
 * Java8 不用Lambda的写法
 */
public void method_1() {

    new Thread(new Runnable() {

        @Override
        public void run() {
            System.out.println("do something..");
        }
    }).start();

}

/**
 * Java8 使用Lambda的写法
 */
public void method_2() {

    new Thread(() -> System.out.println("do something.."));

}
```

**带参数的情况：**

```java
/**
 * Java8 不用Lambda的写法
 */
public void method_3() {
    JButton show = new JButton("Show");
    show.addActionListener(new ActionListener() {

        @Override
        public void actionPerformed(ActionEvent e) {
            System.out.println("do something..");
        }
    });

}

/**
 * Java8 使用Lambda的写法
 */
public void method_4() {
    JButton show = new JButton("Show");
    show.addActionListener((e) -> System.out.println("do something.."));
}
```