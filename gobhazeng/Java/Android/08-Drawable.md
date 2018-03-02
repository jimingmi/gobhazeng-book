# 08-Drawable

## Drawable类简介

Drawable类是Android中用于处理界面的类，提供美观的界面来提高用户体验。

Drawable类包括shape、selector、layer-list等标签以及它们的子标签，这些标签都存在于Drawable文件夹中，也就是说，Drawable中不仅仅能放置图片，还可以放置与图片、图形相关的一些基本设置。

## Shape

Shape，是最基础的形状定义的工具。单独使用时，shape定义的xml文件存放在drawable目录下；
也可以与其他标签组合使用，此时需要被包含于其他标签文件中。

使用shape可以自定义形状，可以定义下面四种类型的形状，通过android:shape属性指定：

- rectangle：矩形，默认的形状，可以画出直角矩形、圆角矩形、弧形等；

- oval ：椭圆形，用得比较多的是画正圆；

- line ：线形，可以画实线和虚线等；

- ring ：环形，可以画环形进度条等；

> 以及在Shape标签中我们需要经常使用的子标签有：solid、padding、gradient、corners、stroke、size。

**solid：设置形状填充的颜色，只有android:color一个属性**

**padding：设置内容与形状边界的内间距，可分别设置左右上下的距离**

> android:left 左内间距、android:right 右内间距、
> android:top 上内间距、android:bottom 下内间距

**gradient：设置形状的渐变颜色，可以是线性渐变、辐射渐变、扫描性渐变**

> android:type 渐变的类型（linear 线性渐变，默认的渐变类型；radial 放射渐变，设置该项时，android:gradientRadius也必须设置；sweep 扫描性渐变）
> android:startColor 渐变开始的颜色
> android:endColor 渐变结束的颜色、
> android:centerColor 渐变中间的颜色、
> android:angle 渐变的角度，线性渐变时才有效，必须是45的倍数，0表示从左到右，90表示从下到上、
> android:centerX 渐变中心的相对X坐标，放射渐变时才有效，在0.0到1.0之间，默认为0.5，表示正中间
> android:centerY 渐变中心的相对X坐标，放射渐变时才有效，在0.0到1.0之间，默认为0.5，表示正中间
> android:gradientRadius 渐变的半径，只有渐变类型为radial时才使用
> android:useLevel 如果为true，则可在LevelListDrawable中使用

**corners：设置圆角，只适用于rectangle类型，可分别设置四个角不同半径的圆角，当设置的圆角半径很大时，比如200dp，就可变成弧形边**

> android:radius 圆角半径，会被下面每个特定的圆角属性重写
> android:topLeftRadius 左上角的半径
> android:topRightRadius 右上角的半径
> android:bottomLeftRadius 左下角的半径
> android:bottomRightRadius 右下角的半径


**stroke： 设置描边，可描成实线或虚线**

> android:color 描边的颜色
> android:width 描边的宽度
> android:dashWidth 设置虚线时的横线长度
> android:dashGap 设置虚线时的横线之间的距离

**size：设置形状默认的大小，可设置宽度和高度**

> android:width 宽度
> android:height 高度

**Rectangle：**

> rectangle是默认的形状，也是用得最多的形状，多用于一些文字背景、按钮背景、控件或布局背景等

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201205701.png)

**oval：**

> oval用来画椭圆，而在实际应用中，更多是画正圆，比如消息提示，圆形按钮等

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201205901.png)

**line：**

> line主要用于画分割线，是通过stroke和size特性组合来实现。注意：只能画横线、无法画竖线

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201210201.png)

**ring：**

ring是环形形状，在shape根元素中有些属性只适用于ring类型，先过目下这些属性：

> android:innerRadius ：内环的半径
> android:innerRadiusRatio ：浮点型，以环的宽度比率来表示内环的半径，默认为3，表示内环	半径为环的宽度除以3，该值会被android:innerRadius覆盖
> android:thickness ： 环的厚度
> android:thicknessRatio ：浮点型，以环的宽度比率来表示环的厚度，默认为9，表示环的厚度为	环的宽度除以9，该值会被android:thickness覆盖
> android:useLevel ：一般为false，否则可能环形无法显示，只有作为LevelListDrawable使用时	才设为true

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201213801.png)

## Selector

在Drawable中shape只能设置单一的图形形状等属性，而在实际应用中，我们需要很多状态的样式，也就是说在不同的状态下要有不同的形态，举个例子，一个按钮的背景，默认时是一个形状，按下时是一个形状，不可操作时又是另一个形状。有时候，不同状态下改变的不只是背景、图片等，文字颜色也会相应改变。而要解决这样的问题，就要用selector来实现了。

**selector拥有很多的状态，如下：**

> android:state_enabled: 设置触摸或点击事件是否可用状态，一般只在false时设置该属性，表示不可用状态。
> android:state_pressed: 设置是否按压状态，一般在true时设置该属性，表示已按压状态，默认为false。
> android:state_selected: 设置是否选中状态，true表示已选中，false表示未选中。
> android:state_checked: 设置是否勾选状态，主要用于CheckBox和RadioButton，true表示已被勾选，false表示未被勾选
> android:state_checkable: 设置勾选是否可用状态，类似state_enabled，只是state_enabled会影响触摸或点击事件，而state_checkable影响勾选事件。
> android:state_focused: 设置是否获得焦点状态，true表示获得焦点，默认为false，表示未获得焦点。
> android:state_window_focused: 设置当前窗口是否获得焦点状态，true表示获得焦点，false表示未获得焦点，例如拉下通知栏或弹出对话框时，当前界面就会失去焦点；另外，ListView的ListItem获得焦点 时也会触发true状态，可以理解为当前窗口就是ListItem本身。
> android:state_activated: 设置是否被激活状态，true表示被激活，false表示未激活，API Level 11及以上才支持，可通过代码调用控件的setActivated(boolean)方法设置是否激活该控件。
> android:state_hovered: 设置是否鼠标在上面滑动的状态，true表示鼠标在上面滑动，默认为false，API Level 14及以上 才支持。

**文本框选择：**

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201214201.png)

**按钮点击效果：**

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201214601.png)

## layer-list

在Android中，可以使用layer-list将多个drawable按照顺序层叠在一起显示。

**layer-list的item可以通过下面四个属性设置偏移量：**

> android:top ：顶部的偏移量
> android:bottom ：底部的偏移量
> android:left ：左边的偏移量
> android:right ：右边的偏移量

这四个偏移量和控件的margin设置差不多，都是外间距的效果。如何不设置偏移量，前面的图层就完全挡住了后面的图层，从而也看不到后面的图层效果了。

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201214801.png)

## 图片

图片是最常用的drawable资源，格式包括：png(推荐)、jpg(可接受)、gif(不建议)。用图片资源需要根据不同屏幕密度提供多张不同尺寸的图片，它们的关系如下表：

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201214901.png)