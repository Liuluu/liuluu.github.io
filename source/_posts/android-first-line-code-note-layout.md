---
title: 第一行代码 - 布局
date: 2018-01-07 19:19:53
toc: true
categories:
    - Android
tags: 
    - Android
    - 第一行代码
    - 读书笔记
description: 排...排队站好！
---

## 基本布局

### 线性布局：LinearLayout

- android:orientation：指定排列的方向，参数：vertical/horizontal。不指定时，默认的排列方向是horizontal。
- android:layout_gravity：指定控件在布局的对齐方式。可以用“|”分割，同时指定多个参数。

**注意**：
1. 排列方向为horizontal时，内部控件不能将宽度指定为match_parent。vertical同理。
2. 排列方向为horizontal时，只有垂直方向上的对齐方式才会生效。vertical同理。

----

- android:layout_weight：使用比例的方式来指定控件的大小。

系统会先把LinearLayout下所有控件指定的layout_weight指相加，得到一个总值，然后每个控件所占大小的比例就是用该控件的layout_weight值除以刚才算出的总值。

由于使用了android:layout_weight属性，此时控件的宽度不再由android:layout_width来决定。此时将android:layout_width属性指定为0dp是一种比较规范的写法。

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal" 
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    
    <!-- TextView和 Button的layout_weight都为1，即两个控件在水平方向平分宽度 -->
    <TextView
        android:id="@+id/text_view"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="This is TextView"/>

    <Button
        android:id="@+id/button"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="Button" />
</LinearLayout>
```

同样是上面的代码，如果Button的属性改为如下所示：

```xml
<Button
    android:id="@+id/button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Button" />
```
则Button的宽度仍按照wrap_content来计算，而TextView则会占满屏幕所有的剩余空间。

使用第二种方法编写的界面，不仅在各种屏幕的适配方面会非常好，而且看起来也更舒服。


### 相对布局：RelativeLayout

#### 相对父控件布局

- android:layout_alignParentBottom="true"：相对父控件下方
- android:layout_alignParentLeft="true"：相对父控件左方
- android:layout_centerInParent="true"：相对父控件居中显示


#### 相对控件布局

- 相对控件左上方
    - android:layout_above="@id/button"
    - android:layout_toLeftOf="@id/button"
- 相对控件右下方
    - android:layout_below="@id/button"
    - android:layout_toRightOf="@id/button"

效果如下：

![][1]

- 两个控件边缘对齐：
    - android:layout_alignLeft
    - android:layout_alignBottom
    - ....

### 帧布局：FrameLayout

所有控件都默认摆放在布局的左上角。

可以使用layout_gravity属性来指定控件在布局中的对齐方式。


### 百分比布局

为了让百分比布局在所有Android版本上都能使用，需要在app/build.gradle文件中添加百分比布局的依赖。

```java
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:26.1.0'
    implementation 'com.android.support.constraint:constraint-layout:1.0.2'
    compile 'com.android.support:percent:26.1.0'
    testImplementation 'junit:junit:4.12'
}
```

接下来修改activity_main.xml文件中的代码：

```xml
<android.support.percent.PercentFrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/button_2"
        android:text="Button_2"
        android:layout_gravity="left|top"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="100%" />

    <Button
        android:id="@+id/button_3"
        android:text="Button_3"
        android:layout_gravity="right|top"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="100%" />
</android.support.percent.PercentFrameLayout>
```

## 自定义布局

### 引入布局

eg. 自定义一个标题。

1. 新建一个布局title.xml，并设置好标题所需的内容
2. 在活动的xml中引入这个布局
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- 引入title布局 -->
    <include layout="@layout/title" />
    
</LinearLayout>
```
3. 在活动中将系统自带的标题栏隐藏

  ```java
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
    	setContentView(R.layout.activity_main);
    
      ActionBar actionBar = getSupportActionBar();
    
      if (actionBar != null) {
    	    actionBar.hide();
    	}
  }
  ```
```

### 创建自定义控件

- 新建TitleLayout类，继承自LinearLayout。重写LinearLayout中带有两个参数的构造函数。
    - 通过LayoutInflater.from()方法构建出一个LayoutInflater对象
    - 调用LayoutInflater的inflate()方法动态加载一个布局文件

​```java 
public class TitleLayout extends LinearLayout {
    public TitleLayout (Context context, AttributeSet attrs) {
        super(context, attrs);
        LayoutInflater.from(context).inflate(R.layout.title, this);
    }
}
```

- 在布局文件中添加这个自定义控件。添加自定义控件时，需要指明控件的完整类名，包名在这里不可以省略。

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!--<include layout="@layout/title" />-->

    <com.example.lll.uitest.TitleLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```
重新运行程序，此时效果和使用引入布局方式的效果是一样的。

- 修改TitleLayout类，为标题栏中的按钮注册点击事件。

这样，每当在一个布局中引入TitleLayout时，返回按钮和编辑按钮的点击事件就已经自动实现好了。

[1]: /img/FirstLineInCode/相对布局.jpg