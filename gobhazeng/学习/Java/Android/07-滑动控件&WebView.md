# 07-滑动控件&WebView

## 滑动控件

### ScrollView

ScrollView是一个 垂直滚动视图，可以滚动的布局容器，可以向用户展示超出设备屏幕的内容。当拥有很多内容，屏幕显示不完时，就需要通过滚动显示完成内容。

> 注意事项：ListView不需要使用ScrollView ，因为ListView自身带有滚动功能。

```xml
	<ScrollView
        android:id="@+id/myScrollView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_b## GridView概述

GridView是一个ViewGroup(布局空间)，它是以表格形式显示多张图片的组件，自带滚动效果。它是按照行列的方式来显示内容的，比如实现九宫格图，用GridView是首选。

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171125194301.png)

### GridView 常用属性

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171125194401.png)

## GridView的使用

GridView的使用类似于ListView。首先准备数据源，数据源中包含图片和文字信息；然后新建适配器，适配器中引入单元格布局文件；在视图界面加载适配器；最后配置事件监听。

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171125194601.png)

## SharedPreferenceActivity

安卓系统保存数据的对象之一

> 特点：存储少量，简单的数据（一般存，配置信息） 它是以XML方式保存数据

### 使用方式

```java
//获取
SharedPreferences sp = getSharedPreferences("cy43",MODE_PRIVATE);
//保存原来的数据
String old = sp.getString("msg","");
//取得编辑器
Editor ed = sp.edit();
//存入新数据
ed.putString("msg",old+str+"#");
//提交
ed.commit();
```

## Demo

### 获取数据页面
```java
import android.app.Activity;
import android.content.SharedPreferences;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.GridView;
import android.widget.TextView;

import com.gobha.myapplication.R;

public class ShowMessage extends Activity {

    private String[] tempArr;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_show_message);

        SharedPreferences sp = getSharedPreferences("cy43",MODE_PRIVATE);
        String str = sp.getString("msg","");
        if(str!=null||str!=""){
            tempArr = str.split("#");
        }

        GridView gv = (GridView) findViewById(R.id.show_message);
        gv.setAdapter(new GridAdapter());
    }

    class GridAdapter extends BaseAdapter {
        @Override
        public int getCount() {
            return tempArr.length;
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
        public View getView(int position, View convertView, ViewGroup parent) {
            View v = View.inflate(ShowMessage.this, R.layout.show_message_item, null);
            TextView weightView = (TextView) v.findViewById(R.id.weight);
            TextView datetimeView = (TextView) v.findViewById(R.id.datetime);
            if(tempArr!=null){
                String[] temp = tempArr[position].split(",");
                String weight =temp[0];
                String datetime =temp[1];
                weightView.setText(weight);
                datetimeView.setText(datetime);
            }
            return v;
        }
    }
}
```

### 存入数据页面

```java
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.EditText;
import android.widget.Toast;

import java.util.Date;

public class SharedPreferenceActivity extends AppCompatActivity {

    private String weight;
    private String datetime;
    private String str;



    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_shared_preference);
    }

    public void saveWeight(View view){
        EditText et = (EditText) findViewById(R.id.weight);
        weight = et.getText().toString();
        datetime = new Date().toString();
        str = weight+","+datetime;

        //安卓系统保存数据的对象之一
        //特点：存储少量，简单的数据（一般存，配置信息） 它是以XML方式保存数据
        SharedPreferences sp = getSharedPreferences("cy43",MODE_PRIVATE);
        String old = sp.getString("msg","");
        Editor ed = sp.edit();
        ed.putString("msg",old+str+"#");
        ed.commit();
        Toast.makeText(SharedPreferenceActivity.this, "保存成功", Toast.LENGTH_SHORT).show();

    }

}
```
elow="@+id/myHorizontalScrollView">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <ImageView
                android:layout_width="300dp"
                android:layout_height="300dp"
                android:layout_marginBottom="50dp"
                android:src="@drawable/jpg_01" />

            <ImageView
                android:layout_width="300dp"
                android:layout_height="300dp"
                android:layout_marginBottom="50dp"
                android:src="@drawable/jpg_01" />

            <ImageView
                android:layout_width="300dp"
                android:layout_height="300dp"
                android:layout_marginBottom="50dp"
                android:src="@drawable/jpg_01" />
        </LinearLayout>
    </ScrollView>
```

#### 操作ScrollView

ScrollView使用比较简单，只需要将超出屏幕范围的子元素，装载到ScrollView中即可，但是这里要切记，ScrollView只能放入**一个子元素**，所有要实现左边的多图片控件摆放，需要将多个控件先放入一个垂直的LinearLayout中，然后再将LinearLayout放入ScrollView里面。

#### 滚动条scrollbar

ScrollView的滚动条，也就是scrollbar，可以设置android:scrollbars为none，此时滚动条即可隐藏。

#### 移动ScrollView

ScrollView其中有两个方法：scrollTo和scrollBy，它们都有两个参数，代表的是X轴和Y轴。

- scrollTo是以左上为原点，定位到某个位置，有点类似于HTML中a标签的锚链接。无论执行多少次都是同一个位置。

- scrollBy是以当前位置为原点，那么每一次执行，都会去寻找下一个移动位置。

```java
ScrollView sv = (ScrollView) findViewById(R.id.myScrollView);
sv.scrollTo(0,500);//定位到某个位置
sv.scrollBy(0,200);//移动某段距离
```

### HorizontalScrollView

ScrollView在使用上，只能进行纵向的滑动，如果想要使用横向滑动，那么就要使用HorizontalScrollView。

> 该控件只支持水平滑动，并且使用的方法以及监听判断等，均和ScrollView相同。

```xml
	<HorizontalScrollView
        android:id="@+id/myHorizontalScrollView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/myButton">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">
            <ImageView
                android:layout_width="200dp"
                android:layout_height="200dp"
                android:layout_marginRight="50dp"
                android:src="@drawable/jpg_03" />

            <ImageView
                android:layout_width="200dp"
                android:layout_height="200dp"
                android:layout_marginRight="50dp"
                android:src="@drawable/jpg_03" />

            <ImageView
                android:layout_width="200dp"
                android:layout_height="200dp"
                android:layout_marginRight="50dp"
                android:src="@drawable/jpg_03" />
        </LinearLayout>
    </HorizontalScrollView>
```

## 滑动监听

ScrollView也有监听，可以监听它的滑动状态。在监听中，能够监听到动作事件：MotionEvent：

- `MotionEvent.ACTION_UP`:   手指抬起事件

- `MotionEvent.ACTION_MOVE`:   手指移动事件

- `MotionEvent.ACTION_DOWN`:   手机按下事件

对于滑动监听，能够做监听滑动是否到底的功能：滑动到底，其实就是检测滑动的距离。如果：滑动距离+屏幕大小=ScrollView全长，那么此时就能判断出已经到底了。

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/20171125220101.png)

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.MotionEvent;
import android.view.View;
import android.widget.ScrollView;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {
    private ScrollView sv;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        sv = (ScrollView) findViewById(R.id.myScrollView);
        sv.setOnTouchListener(new OnTouchListener());
    }

    public void run(View view) {
        sv.scrollBy(0, 1000);
    }

    class OnTouchListener implements View.OnTouchListener {
        @Override
        public boolean onTouch(View v, MotionEvent event) {
            int totalHeight = sv.getChildAt(0).getHeight();//LinearLayout高度
            int svHeight = sv.getHeight();//ScrollView高度
            int distance = sv.getScrollY();//滚动的高度
            switch (event.getAction()) {
                case MotionEvent.ACTION_UP:
                    if ((svHeight + distance) >= totalHeight) {
                        Toast.makeText(MainActivity.this, "已经到达底部", Toast.LENGTH_SHORT).show();
                        break;
                    }
                case MotionEvent.ACTION_DOWN:
                    break;
                case MotionEvent.ACTION_MOVE:
                    break;

            }
            return false;//false 事件到此结束，不在向下继续触发
        }
    }
}
```

## WebView

目前很多android app都内置了可以显示web页面的界面，是由WebView渲染出来的

**优点：**

- 可以直接显示和渲染web页面，直接显示网页

- webview可以直接用html文件（网络上或本地assets中）作布局

- 能和JavaScript交互调用

> 在显示WebView网页内容之前，我们需要在清单文件AndroidManifest.xml中添加允许网络访问权限：`<uses-permission android:name="android.permission.INTERNET"/>`

### WebView的使用

WebView的基本使用方式：

```xml
<WebView
        android:id="@+id/webView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
```

> WebView如果不设置setWebClient方法，则会在安卓系统自带的浏览器中打开该网页，如果设置该方法，那么则会在当前WebView控件位置打开网页。

```java
        wv = (WebView) findViewById(R.id.webView);
        wv.loadUrl("https://www.baidu.com");
        //APP内部浏览器
        wv.setWebViewClient(new WebViewClient(){
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, String url) {
                view.loadUrl(url);
                return super.shouldOverrideUrlLoading(view, url);
            }
        });
```

WebView我们可以使用loadUrl加载网络网页或者assets文件夹中的网页：

- `WebView.loadUrl("http://www.baidu.com");`

- `WebView.loadUrl(“file:///android_asset/xxx.html”);`

若自己定义了一个页面加载进度的progressbar，需要展示给用户的时候，可以通过如下方式获取webview内页面的加载进度。

```xml
<ProgressBar
        style="@style/Base.Widget.AppCompat.ProgressBar.Horizontal"
        android:id="@+id/topProgressBar"
        android:layout_width="match_parent"
        android:progress="0"
        android:layout_height="3dp" />
```

```java
        //浏览器加载进度
        wv.setWebChromeClient(new WebChromeClient(){
            @Override
            public void onProgressChanged(WebView view, int newProgress) {
                pb.setProgress(newProgress);
            }
        });
```

### 返回键

如果在WebView控件中打开了网页，并且该网页进行了跳转等操作，此时，我们需要按返回键时不让当前Activity关闭，而是返回到WebView网页中的上一页面。

```java
    //重写返回键 返回上一个页面
    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        WebBackForwardList webBackForwardList = wv.copyBackForwardList();
        if ((keyCode==KeyEvent.KEYCODE_BACK)&&wv.canGoBack()&&webBackForwardList.getCurrentIndex()!=1){
            wv.goBack();
            return true;
        }
        return super.onKeyDown(keyCode, event);
    }
```

### WebView与JavaScript

在WebView中，很多情况下都想让当前的Activity中的数据，与网页进行交互。那么WebView提供了一系列方法，实现数据交互功能。

#### Javascript中获取Android中的数据

**1.需要允许在WebView中执行JavaScript**

`wv.getSettings().setJavaScriptEnabled(true);//允许执行Javascript`

**2.在WebView中调用addJavascriptInterface方法，该方法能够绑定Java对象到JavaScript中以使能在JavaScript中调用Java对象进而调用该对象中的方法。第一个参数为Java对象，第二个参数为Java对象的别名，该别名在JavaScript中被使用**

`webView.addJavascriptInterface(new DataProvider(),"dataProvider");`

**3.需要写出想要传递数据的类，在类中写入想要传递数据的方法，方法的返回值就是想要传递的数据**

```java
class DataProvider{
        @JavascriptInterface
        String getData(){
            return "数据传递成功！";
        }
    }
```

**4.编辑JavaScript中的代码，在window.dataProvider.getData（）中，dataProvider为Activity中定义的别名，getData（）为自定义类中的方法**

```html
<script type="text/javascript">
function f() {
    var str = window.dataProvider.getData();
    document.getElementById("content").innerHTML = str;
}
</script>
<body>
	<input type="button" value="点击" onclick="f()">
	<div id="content"></div>
</body>
```

#### Android中调用JavaScript函数

在WebView上，使用下面的代码，就能调用Javascript中的函数。其中，showData()方法是Javascript函数中的方法。

`webView.loadUrl("javascript:f1()");`

```html
<script type="text/javascript">
function f1() {
    var str = "页面中的方法f1";
    document.getElementById("content").innerHTML = str;
}
</script>
</head>
<body>
	<input type="button" value="点击" onclick="f()">
	<div id="content"></div>
</body>
```