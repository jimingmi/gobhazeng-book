# 06-GridView&SharedPreference

## GridView概述

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
