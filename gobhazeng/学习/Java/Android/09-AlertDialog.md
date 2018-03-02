# 09-AlertDialog

## AlertDialog简介

Android中的对话框形式大致可分为五种：

-  一般对话框形式

-  列表对话框形式

-  单选按钮对话框

-  多选按钮对话框

-  自定义对话框

## AlertDialog常用方法

AlertDialog的构造方法全部是Protected的，所以不能直接通过new一个AlertDialog来创建出一个AlertDialog。要创建一个AlertDialog，就要用到AlertDialog.Builder中的create()方法。其中有以下几个方法：

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201220201.png)

AlertDialog在Android Support Library v22.1中开始提供了Material风格的Dialog控件。

> 普通的包为android.app.Dialog；兼容包为android.support.v7.app.AlertDialog

```java
public void commonDialog(View view){
        android.app.AlertDialog.Builder builder = new android.app.AlertDialog.Builder(this);
        builder.setTitle("普通的弹出框");
        builder.setMessage("这是弹出内容");
        builder.setPositiveButton("确定",null);
        builder.setNegativeButton("取消",null);
        builder.create();
        builder.show();
    }

    public void mdDialog(View view){
        android.support.v7.app.AlertDialog.Builder builder = new android.support.v7.app.AlertDialog.Builder(this);
        builder.setTitle("兼容风格的弹出框");
        builder.setMessage("这是弹出内容");
        builder.setPositiveButton("确定",null);
        builder.setNegativeButton("取消",null);
        builder.create();
        builder.show();
    }
```

```xml
		<Button
            style="@style/myStyle"
            android:text="一般弹出框"
            android:id="@+id/bt01"
            android:onClick="commonDialog"
            />
        <Button
            style="@style/myStyle"
            android:text="兼容风格按钮"
            android:id="@+id/bt02"
            android:onClick="mdDialog"
            />
```

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201220301.png)

----

在弹出框中，有三种按钮：一种是肯定按钮，主要做确定的操作；一种是否定按钮，主要做取消等操作；一种是中性按钮，这种按钮视情况而定，比较中性化，做提示等操作。这些按钮可以放置，也可以不放置，视功能情况而定。

> 在每种按钮中，我们都能通过第二个参数，为该按钮添加点击监听（这里面的OnClickListener接口是DialogInterface接口的内部接口）。

> 在OnClickListener中参数dialog代表的就是该弹出框，而which是固定参数：-1表示确定按钮，-2表示取消按钮，-3表示提示按钮。


```java
builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "确定:"+which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "取消:"+which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.setNeutralButton("提示", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "提示:"+which, Toast.LENGTH_SHORT).show();
            }
        });
```

----

AlertDialog设定自定义的内容，使用setView方法可以实现该操作

> setView方法可以直接创建原生控件或自定义控件于dialog中，也可以引入其他布局文件到该页面中，引入方式与ListView适配器方式完全一样。

```java
public void commonAlertDialog(View view) {
        android.support.v7.app.AlertDialog.Builder builder = new android.support.v7.app.AlertDialog.Builder(this);
        builder.setTitle("用户登录");
        builder.setMessage("请输入信息：");
        dialogView = View.inflate(DialogActivity.this, R.layout.dialog_item, null);
        builder.setView(new EditText(this));//只设置输入框
        builder.setView(dialogView);
        builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                String username = ((EditText) dialogView.findViewById(R.id.username)).getText().toString();
                String password = ((EditText) dialogView.findViewById(R.id.password)).getText().toString();
                Log.v("login", "用户名：" + username + "，密码：" + password);
            }
        });
        builder.setNegativeButton("取消", null);
        builder.create();
        builder.show();
    }
```

```xml
<EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dp"
        android:layout_marginRight="20dp"
        android:id="@+id/username"
        android:text=""
        android:hint="用户名"
        />

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dp"
        android:layout_marginRight="20dp"
        android:inputType="textPassword"
        android:ems="10"
        android:id="@+id/password"
        android:hint="密码"
        />
```

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201221501.png)

----

AlertDialog中我们使用setMultiChoiceItems为其添加多选框，第一个参数为要现实的项；第二个参数为哪些为选中；第三个参数为为该多选框列表添加点击监听，其中which能够知道这些多选按钮的数组位置。

```java
public void commonDialog(View view) {
        android.app.AlertDialog.Builder builder = new android.app.AlertDialog.Builder(this);
        builder.setTitle("普通的弹出框");
        //builder.setMessage("这是弹出内容");
        builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "确定:" + which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "取消:" + which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.setMultiChoiceItems(new String[]{"西瓜", "苹果", "桔子"}, new boolean[]{true, false, false}, new DialogInterface.OnMultiChoiceClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which, boolean isChecked) {
                Toast.makeText(DialogActivity.this, which + " " + isChecked, Toast.LENGTH_SHORT).show();
            }
        });
        builder.create();
        builder.show();
    }
```

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201221901.png)

----

AlertDialog添加单选框，第一个参数为要现实的项；第二个参数为哪个默认选中；第三个参数为为该多选框列表添加点击监听，其中which能够知道这些多选按钮的数组位置。

```java
public void mdDialog(View view) {
        android.support.v7.app.AlertDialog.Builder builder = new android.support.v7.app.AlertDialog.Builder(this);
        builder.setTitle("兼容风格的弹出框");
        //builder.setMessage("这是弹出内容");
        builder.setSingleChoiceItems(new String[]{"男", "女"}, 0, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "选项:" + which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "确定:" + which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "取消:" + which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.setNeutralButton("提示", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "提示:" + which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.create();
        builder.show();
    }
```

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201223201.png)

> 不要在单选列表和多选列表的弹出框中使用setMessage，也就是不要设置内容信息，选择列表无法与内容信息共存，会导致程序崩溃。

----

与单选和多选很类似，弹出框提供setItems方法，能够设置弹出框显示列表，并且点击列表上的每个item都能触发类似于ListView一样的点击效果。

```java
public void mdDialog(View view) {
        android.support.v7.app.AlertDialog.Builder builder = new android.support.v7.app.AlertDialog.Builder(this);
        builder.setTitle("兼容风格的弹出框");
        //builder.setMessage("这是弹出内容");
        builder.setItems(new String[]{"初中", "高中", "大学"}, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "结果:" + which, Toast.LENGTH_SHORT).show();
            }
        })
        builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "确定:" + which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "取消:" + which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.setNeutralButton("提示", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Toast.makeText(DialogActivity.this, "提示:" + which, Toast.LENGTH_SHORT).show();
            }
        });
        builder.create();
        builder.show();
    }
```

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201223701.png)

----

在Dialog中我们也可以使用setIcon设置图标，效果如下（建议使用背景透明的.png图片）。

```java
public void commonDialog(View view){
        android.app.AlertDialog.Builder builder = new android.app.AlertDialog.Builder(this);
        builder.setTitle("警告");
        builder.setMessage("你是否要退出？");
        builder.setIcon(R.drawable.icon);
        builder.setPositiveButton("确定",null);
        builder.setNegativeButton("取消",null);
        builder.create();
        builder.show();
    }
```

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171201224601.png)