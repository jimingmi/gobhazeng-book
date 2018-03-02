# 02-Android常用控件

## TextView

TextView类继承自View类，TextView控件的功能是向用户显示文本的内容，但不允许编辑。

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171205214301.png)

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171205214401.png)

**android:layout_width和android:width区别**

layout_width属性主要是相对于父控件的大小距离，一般的值为wrap_content、match_parent，也可以设置数值

**如果想使用android:ellipsize实现滚动效果需要如下设置： **

> android:ellipsize="marquee"
> android:focusableInTouchMode="true"
> android:focusable="true”

## Button

Button类继承自TextView类， Button控件是一个文本标签

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171205214001.png)

Button按钮上，其实最主要要实现的是**点击按钮触发相应的事件**，以实现某些想要的功能和效果。在按钮上其实一共有两大种设置监听的方式：

- 1.直接在按钮上设置onClick属性，该属性的值其实就是对应的activity中的同名方法，该方法必须权限为共有，且参数个数为一个，参数类型为View。

```xml
<Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="确定"
        android:onClick="btn"
        />
```

```java
public void btn(View view){
        Toast.makeText(Main2Activity.this, "点击", Toast.LENGTH_SHORT).show();
    }
```

- 2.实现OnClickListener监听接口，并且实现里面的方法onClick。然后通过findViewById方法根据组件ID获取Button按钮，为按钮添加点击监听。

```xml
<Button
        android:id="@+id/btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="确定"
        android:onClick="btn"
        />
```

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

public class Main2Activity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main2);

        //通过id获取Button按钮
        Button button = (Button) findViewById(R.id.btn);
        //为按钮设置监听
        button.setOnClickListener(new BtnOnClickListener());
    }

    class BtnOnClickListener implements View.OnClickListener{
        @Override
        public void onClick(View v) {
            Toast.makeText(Main2Activity.this, "监听方式点击", Toast.LENGTH_SHORT).show();
        }
    }
}
```

## EditText

EditText类继承自TextView类，EditText与TextView最大的不同就是用户可以对EditText控件进行编辑，同时还可以为EditText控件设置监听器，用来判断用户的输入是否合法。

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171205214801.png)

**补充：**

- 文本类型

 > android:inputType="none"
 >  android:inputType="text"
 > android:inputType="textCapCharacters" 字母大写
 > android:inputType="textCapWords" 首字母大写
 > android:inputType="textCapSentences" 仅第一个字母大写
 > android:inputType="textAutoCorrect" 自动完成
 > android:inputType="textAutoComplete" 自动完成
 > android:inputType="textMultiLine" 多行输入
 > android:inputType="textImeMultiLine" 输入法多行（如果支持）
 > android:inputType="textNoSuggestions" 不提示
 > android:inputType="textUri" 网址
 > android:inputType="textEmailAddress" 电子邮件地址
 > android:inputType="textEmailSubject" 邮件主题
 > android:inputType="textShortMessage" 短讯
 > android:inputType="textLongMessage" 长信息
 > android:inputType="textPersonName" 人名
 > android:inputType="textPostalAddress" 地址
 > android:inputType="textPassword" 密码
 > android:inputType="textVisiblePassword" 可见密码
 > android:inputType="textWebEditText" 作为网页表单的文本
 > android:inputType="textFilter" 文本筛选过滤
 > android:inputType="textPhonetic" 拼音输入
 > android:textColorHint 设置提示hint信息的颜色。 

- 数值类型

> android:inputType="number" 数字
> android:inputType="numberSigned" 带符号数字格式
> android:inputType="numberDecimal" 带小数点的浮点格式
> android:inputType="phone" 拨号键盘
> android:inputType="datetime" 时间日期
> android:inputType="date" 日期键盘
> android:inputType="time" 时间键盘

EditText上主要需要监听填写文本信息的变化，实现TextWatcher接口即可实现：

在EditText上主要监听三种情况：beforeTextChanged、onTextChanged、afterTextChanged。

```xml
<EditText
        android:id="@+id/edit"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        />
```

```java
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.text.Editable;
import android.text.TextWatcher;
import android.widget.EditText;

public class Main2Activity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main2);

        EditText editText = (EditText) findViewById(R.id.edit);
        editText.addTextChangedListener(new EditChangedListener());
    }

    class EditChangedListener implements TextWatcher{

        @Override
        public void beforeTextChanged(CharSequence s, int start, int count, int after) {
            // s:变化前的所有字符串；start：字符开始的位置；count：变化前的总字符数；after：变化后的字符数
            // CharSequence可以使用String.valueOf(s)
        }

        @Override
        public void onTextChanged(CharSequence s, int start, int before, int count) {
            // s:变化后的所有字符串；start：字符开始的位置；count：变化前的总字符数；after：变化后的字符数
        }

        @Override
        public void afterTextChanged(Editable s) {
            // 变化后的所有字符
            // Editable可以使用String.valueOf(s)
        }
    }
}
```

> - beforeTextChanged是文本改变之前的监听
> - onTextChanged是指文本被改变时的监听
> - afterTextChanged是指文本改变完成的监听

## ImageView

ImageView控件是一个图片控件，负责显示图片。

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/20171206230801.png)

**资源目录**

- Eclipse中根据图片的像素大小把文件后缀为png或jpg的图片放在drawable文件夹下，如drawable-hdpi、drawable-ldpi、drawable-mdpi、drawable-xhdpi、drawable-xxhdpi。同一张图片放在不同的drawable文件夹下，显示的时候显示不同大小的图片。
- Android Studio中只有一个drawable文件夹，图片依旧是存储在drawable文件夹中。在资源文件中也出现了很多mipmap（纹理映射技术）文件夹，如mipmap-hdpi，mipmap-mdpi,mipmap-xhdpi，mipmap-xxhdpi，这些文件夹是用来存储当前应用的图标。

> Google官方的解释是性能优化，占用内存小。

**ImageView中有一个重要的属性：android:scaleType**

![](http://ovsf6lwoc.bkt.clouddn.com/image/jpg/20171206231201.png)

**px**

px即像素，1px代表屏幕上一个物理的像素点

px单位不被建议使用，因为同样100px的图片，在不同手机上显示的实际大小可能不同.

> 偶尔用到px的情况，是需要画1像素表格线或阴影线的时候，用其他单位如dp会显得模糊。

**dp**

dp实际是dip（density independent pixel）,独立密度像素，意思就是与density密度（dpi）无关，我使用dp作为单位设置控件，不管你什么屏幕大小，多大的dpi，显示的效果始终保持一致。

## CheckBox

CheckBox和RadioButton控件都只有选中和未选中状态，不同的是RadioButton是单选按钮，而CheckBox是多选。

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171206232002.png)

```xml
<CheckBox
        android:id="@+id/checkbox01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="篮球"
        />

    <CheckBox
        android:id="@+id/checkbox02"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="足球"
        />

    <CheckBox
        android:id="@+id/checkbox03"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="排球"
        />
```

CheckBox的监听只需监听按钮状态即可，注意接口是CompoundBtton下的OnCheckedChangeListener接口

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.widget.CheckBox;
import android.widget.CompoundButton;
import android.widget.Toast;

public class Main3Activity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main3);

        CheckBox checkBox01 = (CheckBox) findViewById(R.id.checkbox01);
        CheckBox checkBox02 = (CheckBox) findViewById(R.id.checkbox02);
        CheckBox checkBox03 = (CheckBox) findViewById(R.id.checkbox03);

        checkBox01.setOnCheckedChangeListener(new CheckBoxCheckedChange());
        checkBox02.setOnCheckedChangeListener(new CheckBoxCheckedChange());
        checkBox03.setOnCheckedChangeListener(new CheckBoxCheckedChange());

    }

    class CheckBoxCheckedChange implements CompoundButton.OnCheckedChangeListener{

        @Override
        public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
            //buttonView表示的是当前的多选按钮
            //isChecked表示当前的多选按钮是否被选中
            // 通过这个方法，来监听当前的checkbox是否被选中
            if (isChecked) {
                //获得CheckBox的文本内容
                String text = buttonView.getText().toString();
                Toast.makeText(Main3Activity.this, text, Toast.LENGTH_SHORT).show();
            }
        }
    }
}
```

## RadioButton

RadioButton需要编制到一个RadioGroup中，同一时刻一个RadioGroup中只能有一个按钮处于选中状态。若想RadioButton实现单选，必须在每一个RadioButton中设置id属性。

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171206232601.png)

单选监听需要加在RadioGroup上，并且要注意是实现RadioGroup类下的OnCheckedChangeListener接口

```xml
<RadioGroup
        android:id="@+id/sex"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        >
        <RadioButton
            android:id="@+id/mal"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="男"
            android:checked="true"
            />
        <RadioButton
            android:id="@+id/femal"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="女"
            />
    </RadioGroup>
```

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.widget.CheckBox;
import android.widget.CompoundButton;
import android.widget.RadioGroup;
import android.widget.Toast;

public class Main3Activity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main3);

        RadioGroup radioGroup = (RadioGroup) findViewById(R.id.sex);
        radioGroup.setOnCheckedChangeListener(new RadioGroupListener());

    }

    class RadioGroupListener implements RadioGroup.OnCheckedChangeListener{

        @Override
        public void onCheckedChanged(RadioGroup group, int checkedId) {
            Toast.makeText(Main3Activity.this, "Group:"+group+" "+"id:"+checkedId, Toast.LENGTH_SHORT).show();
        }
    }
}
```

## ToggleButton

ToggleButton是android提供的开关按钮。

与RadioButton等控件一样，都是继承自android.widget.CompoundButton，也就是说这种控件有两种状态：选中和未选中。

**在ToggleButton中选中为“开”状态，未选中为“关”状态。**

```xml
<ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="false"
        android:textOff="关"
        android:textOn="开"
        />
```

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.widget.CheckBox;
import android.widget.CompoundButton;
import android.widget.RadioGroup;
import android.widget.Toast;
import android.widget.ToggleButton;

public class Main3Activity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main3);

        ToggleButton toggleButton = (ToggleButton) findViewById(R.id.switchButton);
        toggleButton.setOnCheckedChangeListener(new ToggleButtonCheckedChange());

    }

    class ToggleButtonCheckedChange  implements CompoundButton.OnCheckedChangeListener{

        @Override
        public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
            Toast.makeText(Main3Activity.this, buttonView.getText().toString(), Toast.LENGTH_SHORT).show();
        }
    }
}
```

## ProgressBar

ProgressBar 是进度条组件，通常用于向用户展示某个耗时操作完成的进度，而不让用户感觉是程序失去了响应，从而更好地提升用户界面的友好性。

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171209220701.png)

**样式**

Style 属性来给 ProgressBar 确定样式，有两种方式。

- 第一种就是 API文档中说明的方式：

```xml
Widget.ProgressBar.Horizontal
Widget.ProgressBar.Small
Widget.ProgressBar.Large
Widget.ProgressBar.Inverse
Widget.ProgressBar.Small.Inverse
Widget.ProgressBar.Large.Inverse
```

设置的方式：`style="@android:style/Widget.ProgressBar.Horizontal"`

- 第二种就是使用系统的attr，下面的方式是系统的style：

```xml
progressBarStyle
progressBarStyleHorizontal
progressBarStyleInverse
progressBarStyleLarge
progressBarStyleLargeInverse
progressBarStyleSmall
progressBarStyleSmallInverse
progressBarStyleSmallTitle
```

设置的方式：`style="?android:attr/progressBarStyleHorizontal" `

----

系统自带的样式中最常用的就是以下三种了，分别代表小环形进度条，大环形进度条，水平进度条。

这里并没有中型环形进度条，因为如果不设置 style 属性或者设置为 progressBarStyle 就显示为中等大小。

- style=”?android:attr/progressBarStyleSmall”
- style=”?android:attr/progressBarStyleLarge”
- style=”?android:attr/progressBarStyleHorizontal”

----

**分类**

ProgressBar分为精确的和不精确的：

- 精确的是我们能明确看到进度（可以显示刻度或者百分比）。
- 不精确的就是不确定一个操作需要多长时间来完成，这个时候就需要用的就是不精确的ProgressBar了，比如过场动画，刷新进度。


## SeekBar

SeekBar是Android的拖动条。它与滚动条很像，在进度条上有可拖动按钮。主要是应用在视频或音频等多媒体的进度。

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171209233801.png)

SeekBar可以添加SeekBar.OnSeekBarChangeListener监听，主要监听当前的进度情况。

```xml
<SeekBar
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:max="100"
        android:progress="50"
        />
```

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.widget.SeekBar;

public class Main4Activity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main4);

        SeekBar seekBar = (SeekBar) findViewById(R.id.seekBar);
        seekBar.setOnSeekBarChangeListener(new SeekBarChangeListener());

    }

    class SeekBarChangeListener implements SeekBar.OnSeekBarChangeListener {

        @Override
        //拖动过程中
        public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
            Log.v("SeekBarChangeListener", String.valueOf(progress) + "," + String.valueOf(fromUser));
        }

        @Override
        //开始过程中
        public void onStartTrackingTouch(SeekBar seekBar) {
            Log.v("SeekBarChangeListener", String.valueOf(seekBar.getProgress()));
        }

        @Override
        //结束拖动中
        public void onStopTrackingTouch(SeekBar seekBar) {
            Log.v("SeekBarChangeListener", String.valueOf(seekBar.getProgress()));
        }
    }

}
```

## DatePicker&TimePicker

DatePicker继承自FrameLayout类，日期选择控件的主要功能是向用户提供包含年、月、日的日期数据并允许用户对其修改。如果要捕获用户修改日期选择控件中的数据事件，需要为DatePicker添加OnDateChangedListener监听器。

TimePicker也继承自FrameLayout类。时间选择控件向用户显示一天中的时间（可以为24小时，也可以为AM/PM制），并允许用户进行选择。如果要捕获用户修改时间数据的事件，便需要为TimePicker添加OnTimeChangedListener监听器

![](http://ovsf6lwoc.bkt.clouddn.com//image/jpg/20171209234901.png)

```xml
<DatePicker
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:datePickerMode="spinner"
            ></DatePicker>
        <DatePicker
            android:layout_width="wrap_content"
            android:layout_height="100dp"
            android:datePickerMode="calendar"
            ></DatePicker>
```