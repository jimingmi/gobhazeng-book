# 05-ListView

## ListView

ListView是比较常用的组件，它以列表的形式展示具体内容，并且能够根据数据的长度自适应显示。

**ListView中的每一个条目都是一个View对象**

```xml
<ListView
        android:id="@+id/lv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
</ListView>
```

> ListView中，我们使用android:divider属性设置Item与Item之间的分界线，若想去掉分界线，则直接赋值为“@null”。我们也同样可以使用android:dividerHeight属性设置分界线的高度（也就是粗细）。

## Adapter

一个Adapter是AdapterView视图与数据之间的桥梁，Adapter提供对数据的访问，也负责为每一项数据产生一个对应的View。
官方定义翻译为普通话的意思就是，Adapter是用来帮助填充数据的中间桥梁，可以通过它将数据填充到ListView。

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/20171125192001.png)

在使用ListView时，ListView里的数据需要通过Adapter将每一条数据直充到ListView当中，这时我们就需要创建Adapter对象，但Adapter是一个接口，所以我们需要通过Adapter接口的实现类来创建Adapter对象。

> Adapter（适配器）的实现类有很多，BaseAdapter、CursorAdapter、HeaderViewListAdapter、ResourceCursorAdapter、SimpleAdapter、SimpleCursorAdapter等，这些实现类中最常用的就是BaseAdapter。

BaseAdapter的四个方法是必需要重写的，其中getCount()、getView()这两个方法是由系统自动调用的，并通过这两个方法完成对ListView的数据填充，而getItem()、和getItemId()这两个方法是用户在使用时调用，所以这两个方法一般情况下可以不理会。

```java
import android.os.Bundle;
import android.support.design.widget.FloatingActionButton;
import android.support.design.widget.Snackbar;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.ListView;
import android.widget.TextView;

import java.util.ArrayList;

import util.People;

public class ListViewActivity extends AppCompatActivity {

    ArrayList<People> arr;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        arr = new ArrayList<People>();

        /*虚拟数据*/
        for (int i = 0; i < 20; i++) {
            arr.add(new People("张三" + i, "我是一个人，我是一个人啊" + i, "男"));
        }

        ListView lv = (ListView) findViewById(R.id.lv);
        lv.setAdapter(new MainAdapter());
    }

    class MainAdapter extends BaseAdapter {//内部类,可以使用外部的成员变量

        @Override
        public int getCount() {//元素个数
            return arr.size();
        }

        @Override
        public Object getItem(int position) {
            return null;
        }

        @Override
        public long getItemId(int position) {
            return 0;
        }

        @Override
        public View getView(int position, View convertView, ViewGroup parent) {//适配器会调用getView生成元素

            //给他想要的元素
            //获取布局文件，填充数据，返回结果
            //View是所有控件父类
            View v = View.inflate(ListViewActivity.this, R.layout.item_layout, null);
            TextView name = (TextView) v.findViewById(R.id.name);
            TextView message = (TextView) v.findViewById(R.id.message);
            People p = arr.get(position);
            name.setText(p.getName());
            message.setText(p.getMessage());
            return v;
        }
    }

}
```

### Adapter对应的布局

这个布局文件是对应Adapter的，主要的作用是为了用户可以很好的展示数据，用户可以通过单独的布局文件来规划每个View对象（条目）的样式，这也是我们在新闻类手机应用中看到的每个条目中都包涵了图片、文字、内容等多种数据信息的实现方式。

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:src="@drawable/jpg_01"
        android:layout_width="200px"
        android:layout_height="200px"
        android:id="@+id/imageView"
        android:layout_alignParentTop="true"
        android:layout_alignParentStart="true" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="姓名"
        android:id="@+id/name"
        android:layout_alignParentTop="true"
        android:layout_toRightOf="@+id/imageView"
        android:layout_marginTop="10dp"
        android:textSize="30dp"

        />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="信息"
        android:id="@+id/message"
        android:layout_below="@+id/name"
        android:layout_alignStart="@+id/name"
        android:layout_marginTop="10dp"
        android:textSize="20dp"

        />

</RelativeLayout>
```

之后使用BaseAdapter完成对ListView的数据填充

> ListView自带滑动功能并且在ListView中的每一个View（条目）都自动增加一条分隔线。

## 其他适配器

### ArrayAdapter

ArrayAdapter是单一数据适配器，ArrayAdapter期望接受的样式文件里只含有一个TextView，由于这个适配器封装度比较高所以使用时没有BaseAdapter灵活，但效率比较高。

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/20171125193401.png)

### SimpleAdapter

SimpleAdapter可以同时处理多种数据，可以处理比ArrayAdapter更复杂的案例；

- 上下文对象；
- 数据项；
- 布局文件的资源ID；
- 数据项中key的名称；
- 布局文件中用于显示对应数据的控件的资源ID；

**注意的是map对象的key可以找不到，因为找不到key也会返回null其实就相当于给了一个null资源**

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171125200301.png)

## ListView监听器

ListView显示数据只是一部分功能而已，在功能上来讲，ListView不仅能让用户看到数据，也要能让用户执行点击操作。在ListView的监听器中，我们使用OnItemClickListener和OnItemLongClickListener监听用户对Item的操作。

```java
ListView lv = (ListView) findViewById(R.id.lv);
lv.setAdapter(new MainAdapter());//设置适配器
lv.setOnItemClickListener(new AdapterView.OnItemClickListener(){//设置单击事件

  @Override
  public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
    Log.v("OnItemClickListener","单击事件——你点击我了:"+position);
  }
});
lv.setOnItemLongClickListener(new AdapterView.OnItemLongClickListener(){//设置长按事件

  @Override
  public boolean onItemLongClick(AdapterView<?> parent, View view, int position, long id) {
    Log.v("OnItemClickListener","长按事件——你点击我了:"+position);
    return false;//返回值是false，表示的是触发了长按事件后，接下来还会触发单击事件；返回值是true表示的是长按事件结束后，不会触发其他事件
  }
});
```

> position参数代表当前是第几个Item；OnItemLongClickListener的返回值如果是false，表示的是触发了长按事件后，接下来还会触发单击事件；返回值是true表示的是长按事件结束后，不会触发其他事件