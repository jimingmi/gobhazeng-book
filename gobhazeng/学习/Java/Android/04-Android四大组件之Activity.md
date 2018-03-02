# 04-Android四大组件之Activity

## Activity简介

Activity是Android系统中的四大组件之一，可以用于显示View。Activity是一个与用户交互的系统模块，几乎所有的Activity都是和用户进行交互的。

在Android中Activity主要是用来做控制的，它可以选择要显示的View，也可以从View中获取数据然后把数据传给Model层进行处理，最后再来显示出处理结果。

## 创建Activity

创建一个Activity，必须创建一个Activity的子类（或者一个Activity的子类的子类，例如FragmentActivity等），也就是说使用普通类继承Activity 类即可。

之后需要重写Activity的方法`Oncreate()`，并且在方法中调用`setContentView()`方法，参数为`R.layout.布局文件名`，此时该Activity在被启动时即可加载一个可见的视图在当前手机的屏幕上。

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.Window;
import android.view.WindowManager;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}
```

最后需要在AndroidManifest.xml清单文件上注册该Activity，这样在工程启动时Activity即可准备就绪以备使用。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.gobha.class2">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
        	<intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

> R.java资源文件：
> - android工程所有资源信息(组件、图片、字符等等)都是由HashMap<Integer,Object>来存储的，key值就是R.java中的静态变量值（为16进制的int型数据），value就是相对应的各种对象信息(组件、图片、字符等等)。当工程被载入时，所有资源都被加载到HashMap中去(类似Spring将所有Bean放入HashMap中一样)，这样我们就可通过key值定位到相应的组件。 

> 资源加载的大致过程：
> - 1.解析res文件夹下的各种文件,如果是xml文件调用java反射机制解析成相应的对象。
> - 2.由res文件夹下文档的目录层级结构来匹配R.java中相对应的静态变量名称,并取出变量值作为key值。
> - 3.将步骤2中key值和步骤1中的value值放入到`HashMap<Integer,Object>`中.而Context类中的`getResource()`方法实际上就是返回这个HashMap对象。

## Intent意图

若要实现Activity之间的跳转，那么就要借助Intent类。

Android应用将会根据Intent来启动指定组件，通过Intent传递信息。
最常见的运用场景是启动Activity。

### Intent跳转

**思路：**

- 1.创建2个Activity，分别对应2个布局文件
- 2.在第1个页面布局文件中添加1个button
- 3.点击第1个页面按钮，跳转到第2个页面

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/20171211175801.png)

**代码实现：**

```java
import android.content.Intent;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;

public class FirstActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_first);

        Button button = (Button) findViewById(R.id.toNextActivity);
        assert button != null;
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //实例化一个Intent对象，在实例化过程中显示的指明要启动的Activity
                Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
                //通过startActivity()函数启动
                startActivity(intent);
            }
        });
    }
}
```

### Intent种类

在Android中，Intent分为如下两种情况：

**显式的Intent（常用）**

即在构造Intent对象时就指定接收者，它一般用在知道目标组件名称的前提下，一般是在相同的应用程序内部实现的。

`Intent intent = new Intent(FirstActivity.this, SecondActivity.class);`

**隐式的Intent**

即Intent的发送者在构造Intent对象时，并不知道也不关心接收者是谁，有利于降低发送者和接收者之间的耦合，它一般用在没有明确指出目标组件名称的前提下，一般是用于在不同应用程序之间。隐式意图需要在AndroidMainfest.xml中为Activity配置Intent-Filter子节点。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.gobha.class2">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
        	<intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

- `android.intent.action.MAIN`决定一个应用程序最先启动那个组件
- `android.intent.category.LAUNCHER`决定应用程序是否显示在程序列表里(即是否显示应用图标)

## Activity生命周期

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/20171211190601.png)

- 1.启动Activity：系统会先调用`onCreate()`方法，然后调用`onStart()`方法，最后调用`onResume()`，Activity进入运行状态。
- 2.当前Activity被其他Activity覆盖其上或被锁屏：系统会调用`onPause()`方法，暂停当前Activity的执行。
- 3.当前Activity由被覆盖状态回到前台或解锁屏：系统会调用`onResume()`方法，再次进入运行状态。
- 4.当前Activity转到新的Activity界面或按Home键回到主屏，自身退居后台：系统会先调用`onPause()`方法，然后调用`onStop()`方法，进入停滞状态。
- 5.用户后退回到此Activity：系统会先调用`onRestart()`方法，然后调用`onStart()`方法，最后调用`onResume()`方法，再次进入运行状态。
- 6.当前Activity处于被覆盖状态或者后台不可见状态，即第2步和第4步，系统内存不足，杀死当前Activity，而后用户退回当前Activity：再次调用`onCreate()`方法、`onStart()`方法、`onResume()`方法，进入运行状态。
- 7.用户退出当前Activity：系统先调用`onPause()`方法，然后调用`onStop()`方法，最后调用`onDestory()`方法，结束当前Activity。

**注意：**

通过创建两个Activity，并在两个Activity中都重写右侧图中的七个方法，通过两个Activity之间的切换显示（需要在两个Activity的布局中添加按钮，用户触发第二个Activity的显示），配合手机的Home键可以看到完整的Activity的生命周期。

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;

public class LifecycleActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_lifecycle);
        Log.v("test","onCreate方法，Activity被创建时调用");
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.v("test","onStart方法，Activity能被看见，但没有获得焦点时调用");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.v("test","onResume方法，Activity能被看见并获得焦点时调用，此时被称为前台Activity");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.v("test","onPause方法，Activity失去焦点，但Activity仍然可见");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.v("test","onStop方法，Activity不可见");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.v("test","onDestroy方法，Activity已被销毁，内存中已经不存在");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.v("test","onRestart方法，没被销毁的Activity重新回到前台时调用");
    }
}
```

> Activity在横竖屏切换时该Activity会被销毁重建。
> 修改AndroidManifest.xml文件中Activity节点的属性让系统在用户做横竖屏切换时不触发生命周期方法

```xml
<activity
            android:name=".MainActivity"
            android:configChanges="keyboardHidden|orientation|screenSize"
            >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
```

## Activity启动模式

Activity有四种启动模式，分别是：

- **standard**
  - 每次激活Activity时(startActivity)，都创建Activity实例，并放入任务栈。
- **singleTop**
  - 如果某个Activity自己激活自己，即任务栈栈顶就是该Activity，则不需要创建。
- **singleTask**
  - 如果要激活的那个Activity在任务栈中存在该实例，则不需要创建，只需要把此Activity放入栈顶，并把该Activity以上的Activity实例都pop。
- **singleInstance**
  - 设为此模式的Activity会有一个自己独立的任务栈，该Activity的实例只会创建一个，保存在独立的任务栈 中；保证整个系统的内存中只有一个该Activity的实例。


**图文说明：**

第一种：standard（默认情况）

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/20171211213901.png)

第二种：singleTop（当Activity2被设置为singleTop）

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171211214001.png)

第三种：singleTask（当Activity1被设置为singleTask ）

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171211214201.png)

第四种：singleInstance（当Activity2被设置为singleInstance ）

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171211214401.png)

## Intent传值

在Intent跳转中，可以利用Bundle来进行传值。

**Bundle简介:**

> Bundle主要用于传递数据；它保存的数据，是以key-value(键值对)的形式存在的。
> 我们经常使用Bundle在Activity之间传递数据，传递的数据可以是boolean、byte、int、long、float、double、string等基本类型或它们对应的数组，也可以是对象或对象数组。

**Intent本身可以传值，为什么还要用到Bundle呢？**

> 因为Intent中内置了一个默认的Bundle，所以Intent可以实现简单写法的传值功能，但是有的时候是必须使用Bundle的，比如说Fragment与Fragment之间的传值、设备旋转时保存数据等。

```java
Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
                intent.putExtra("data","数据");
```

### 传递基本数据类型

**传值：**
```java
//实例化一个Intent对象，在实例化过程中显示的指明要启动的Activity
                Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
                //创建Bundle对象，为Bundle添加数据
                Bundle bundle = new Bundle();
                bundle.putString("name", "张三");
                bundle.putInt("age", 20);
                bundle.putChar("sex", '男');
                //将Bundle放在Intent中，跳转传递数据
                intent.putExtras(bundle);
                //通过startActivity()函数启动
                startActivity(intent);
```

**取值：**
```java
Intent intent = getIntent();
        Bundle bundle = intent.getExtras();
        String name = bundle.getString("name");
        int age = bundle.getInt("age");
        char sex = bundle.getChar("sex");
        Log.v("test", "name:" + name + ",age:" + age + ",sex:" + sex);
```

### 传递对象或对象数组

当Bundle传递的是对象或对象数组时，必须实现Serializable或Parcelable接口。

> - **1.Serializable和Parcelable的作用**
>   Serializable的作用是为了保存对象的属性到本地文件、数据库、网络流、rmi以方便数据传输，当然这种传输可以是程序内的也可以是两个程序间的。而Android的Parcelable的设计初衷是因为Serializable效率过慢，为了在程序内不同组件间以及不同Android程序间(AIDL)高效的传输数据而设计，这些数据仅在内存中存在，Parcelable是通过IBinder通信的消息的载体。
> - **2.效率及选择**
>   Parcelable的性能比Serializable好，在内存开销方面较小，所以在内存间数据传输时推荐使用Parcelable，如activity间传输数据；而Serializable可将数据持久化方便保存，所以在需要保存或网络传输数据时选择Serializable，因为android不同版本Parcelable可能不同，所以不推荐使用Parcelable进行数据持久化

**传值：**

```java
import java.io.Serializable;

//需要实现Serializable接口
public class People implements Serializable {
    private String name;
    private int age;
    private char sex;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public char getSex() {
        return sex;
    }

    public void setSex(char sex) {
        this.sex = sex;
    }

    @Override
    public String toString() {
        return "People{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", sex=" + sex +
                '}';
    }
}
```

```java
//实例化一个Intent对象，在实例化过程中显示的指明要启动的Activity
                Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
                People people = new People();
                people.setName("李四");
                people.setAge(18);
                people.setSex('男');
                //创建Bundle对象，为Bundle添加数据
                Bundle bundle = new Bundle();
                bundle.putSerializable("people", people);
                //将Bundle放在Intent中，跳转传递数据
                intent.putExtras(bundle);
                //通过startActivity()函数启动
                startActivity(intent);
```

**取值：**

```java
//获取Intent
        Intent intent = getIntent();
        //从Intent中获取Bundle
        Bundle bundle = intent.getExtras();
        //从Bundle中取出对象
        People people = (People) bundle.getSerializable("people");
        //从对象中取出数据
        String name = people.getName();
        int age = people.getAge();
        char sex = people.getSex();
        Log.v("test", "name:" + name + ",age:" + age + ",sex:" + sex);
```

###  返回Activity传递数据

在跳转中，有时也会有返回数据的需求。如果想在Activity中得到新打开Activity 关闭后返回的数据，需要使用系统提供的`startActivityForResult()`方法打开新的Activity，新的Activity 关闭后会向原Activity传回数据，为了得到传回的数据，必须在原Activity中重写`onActivityResult()`方法。

**原Activity：**

```java
//发送数据
    public void sendData(View view){
        Intent intent = new Intent(FirstActivity.this, Second3Activity.class);
        intent.putExtra("data","数据");
        //第二个参数为requestCode，它可以设置请求跳转的编码，当前的Activity能得到它的值，但新的Activity页面不知道它的值。可以为返回时对应的Activity进行编号，以便知道是谁返回的数据。
        startActivityForResult(intent,0);
    }

    //获取Activity返回的数据
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        Log.v("requestCode",String.valueOf(requestCode));
        Log.v("resultCode",String.valueOf(resultCode));
        Log.v("requestData",data.getStringExtra("returnData"));

    }
```

**新打开的Activity：**

```java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second3);

        //接收到数据
        Intent intent = getIntent();
        String data = intent.getStringExtra("data");
        Log.v("data",data);
    }

    //返回数据
    public void returnData(View view){
        //返回数据同时关闭当前页面
        Intent intent = new Intent();
        intent.putExtra("returnData","返回数据");
        setResult(1,intent);
        finish();
    }
```

- requestCode：它可以设置请求跳转的编码，当前的Activity能得到它的值，但新的Activity页面不知道它的值。好处是为返回时对应的Activity进行编号，以便知道是谁返回的数据。

- resultCode：它可以设置结果编码，它要告诉返回的页面是一个什么样的结果，比如是否登陆成功。
