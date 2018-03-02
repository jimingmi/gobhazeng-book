# 03-Android常用布局

## Android布局关系图

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/20171211145001.png)

## 线型布局-LinearLayout

线性布局，从外框上可以理解为一个div，是一个一个从上往下罗列在屏幕上。每一个LinearLayout里面又可分为垂直布局`android:orientation="vertical"`和水平布局`android:orientation="horizontal" `。

　　当垂直布局时，每一行就只有一个元素，多个元素依次垂直往下；水平布局时，只有一行，每一个元素依次向右排列。

　　LinearLayout中有一个重要的属性` android:layout_weight=“1”`，这个属性的值在垂直布局时代表行距，水平布局的时候代表列宽，layout_weight的值越大，相对的行距（列宽）就越大。
　　
**注意：**

- 线形布局中预览和真机中完全一样；
- 在垂直布局下，左对齐、右对齐、水平居中生效；
- 在水平布局下，顶部对齐、底部对齐、垂直居中生效； 

### 线性布局属性说明

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/20171211152301.png)

## 相对布局-RelativeLayout

相对布局是按照组件之间的相对位置来布局，换句话说就是按照各子元素之间的位置关系完成布局，比如在某个组件的左边、右边、上面或者下面等。

**可以使用子元素里与位置相关的属性：**

- android：layout_below
- android:layout_above
- android:layout_centerVertical

> 相对布局是Android五大布局结构中最灵活的一种布局结构，比较适合一些复杂界面的布局。

**注意：**

- 在指定位置关系时，引用的ID必须在引用之前，先被定义，否则将出现异常。
- 在相对布局中没有权重属性。

### 线性布局属性说明

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171211154101.png)

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171211154201.png)

## TableLayout-表格布局

表格布局适用于N行N列的布局格式。一个TableLayout由许多TableRow组成，一个TableRow就代表TableLayout中的一行，TableRow中的每一个元素就代表一个单元格。

TableRow是LinearLayout的子类，TableLayout并不需要明确地声明包含多少行、多少列，而是通过TableRow，以及其他组件来控制表格的行数和列数， TableRow也是容器，因此可以向TableRow里面添加其他组件，每添加一个组件该表格就增加一列。如果想TableLayout里面添加组件，那么该组件就直接占用一行。在表格布局中，列的宽度由该列中最宽的单元格决定，整个表格布局的宽度取决于父容器的宽度（默认是占满父容器本身）。

> 注：一般TableLayout的布局方式是使用比较少的。

### 表格全局属性说明

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171211155301.png)

### 表格单元格属性说明

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171211160301.png)

## FrameLayout-帧布局

FrameLayout是层布局方式。在这个布局中，整个界面被当成一块空白备用区域，所有的子元素都不能被指定放置的位置，它们统统放于这块区域的左上角，并且后面的子元素直接覆盖在前面的子元素之上，将前面的子元素部分和全部遮挡。

### 帧布局属性说明

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171211160501.png)

## GridLayout-网格布局

GridLayout布局使用虚细线将布局划分为行、列和单元格，也支持一个控件在行、列上都有交错排列。而GridLayout使用的其实是跟LinearLayout类似的API，只不过是修改了一下相关的标签而已。

首先它与LinearLayout布局一样，也分为水平和垂直两种方式，默认是水平布局，一个控件挨着一个控件从左到右依次排列，但是通过指定`android:columnCount`设置列数的属性后，控件会自动换行进行排列。另一方面，对于GridLayout布局中的子控件，默认按照wrap_content的方式设置其显示，这只需要在GridLayout布局中显式声明即可。

其次，若要指定某控件显示在固定的行或列，只需设置该子控件的`android:layout_row`和`android:layout_column`属性即可，但是需要注意：`android:layout_row=”0”`表示从第一行开始，`android:layout_column=”0”`表示从第一列开始，这与编程语言中一维数组的赋值情况类似。

最后，如果需要设置某控件跨越多行或多列，只需将该子控件的`android:layout_rowSpan`或者layout_columnSpan属性设置为数值，再设置其layout_gravity属性为fill即可，前一个设置表明该控件跨越的行数或列数，后一个设置表明该控件填满所跨越的整行或整列。

### 网格布局属性说明

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171211160701.png)

> 注：行和列都是从0开始算的