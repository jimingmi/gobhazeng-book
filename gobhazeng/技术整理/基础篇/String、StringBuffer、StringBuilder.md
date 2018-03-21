# String、StringBuffer、StringBuilder的区别

- String对象是不可改变的，储存在Java的内存模型的常量池中

- 由于String的值一旦创建就不能修改，所以称它是恒定的，看似能修改String的方法实际上只是返回一个包含修改内容的新String。即当String对象被修改后，也就是创建了新的对象被赋值，之前的对象则被GC回收。

- 如果要修改字符串而不创建新的对象，则可以使用StringBuffer类。例如：当在一个循环体中需要将多个字符串拼接在一起，为了避免反复创建对象，使用StringBuffer类可以提升性能。

- StringBuffer 可改变的Unicode字符序列，允许并发操作，线程安全。

- StringBuilder 可改变的Unicode字符序列，操作同StringBuffer，不支持并发操作，非线程安全。

- 不考虑线程安全的前提下，StringBuilder性能最高，StringBuffer次之，String比较差。原因在于String类属于不可变类，任何对String引用指向的字符串作出的修改都会导致生成新的字符串（对象），而对StringBuffer和StringBuilder的修改不会导致新对象的产生。同时StringBuffer是线程安全的，导致其性能低于StringBuilder。