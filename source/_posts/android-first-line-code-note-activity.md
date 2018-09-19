---
title: 第一行代码 - 活动
date: 2018-01-07 12:30:20
toc: true
categories:
    - Android
tags: 
    - Android
    - 第一行代码
    - 读书笔记
description: 先从看得到的入手 - 探究活动
---

定义：包含用户界面的组件。

用途：主要用于和用户进行交互。

## 注册活动、Toast和Menu

### 在AndroidManifest文件中注册

所有活动都要在AndroidManifest中注册才能生效。

**配置主活动：** 在<activity>标签内加入<intent-filter>标签，并添加声明action和category。

android:label用来指定活动中标题栏的内容。 **注意：** 给主活动指定的label还会成为Launcher中应用程序显示的名称。

```java
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.lll.activitytest">

    <application
        ...>
        <activity
            android:name=".FirstActivity"
            android:label="This is FirstActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

### Toast

1. 通过静态方法makeText()方法创建一个Toast对象。参数列表：
    1. Context对象
    2. 显示的文本内容
    3. 显示的时长：有两个内置常量（Toast.LENGTH_SHORT和Toast.LENGTH_LONG）
2. 调用Toast对象的show()方法显示

```java
Toast.makeText(FirstActivity.this, "toast content", Toast.LENGTH_SHORT).show();
```

### Menu

- 在res目录下新建一个menu文件夹，再在文件夹下新建一个叫main的菜单文件(New->Menu resource file)
- 在main.xml下添加如下代码
```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/add_item"
        android:title="Add" />
    <item
        android:id="@+id/remov_item"
        android:title="Remove" />
</menu>
```
<item>标签就是用来创建具体的某一个菜单项。
- 在activity中重写`onCreateOptionsMenu()`方法
```java
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main, menu);
    return true;
}
```
通过`getMenuInflater()`方法能够得到MenuInflater对象，再调用它的`inflate()`方法给当前活动创建菜单。

inflate()方法接受两个参数：1.用于指定通过哪个资源文件来创建菜单；2.用于指定菜单项添加到哪一个menu对象当中。

inflate()返回true，表示允许创建的菜单显示出来，如果返回了false，创建的菜单将无法显示。
- 定义菜单响应事件：onOptionsItemSelected()
```java
public bllean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.add_item:
            ...
            break;
        case R.id.remove_item:
            ...
            break;
        default:
        }
        return true;
    }
}
```

### finish()

销毁活动，效果和按下back键是一样的。

## Intent

Intent是Android程序中各组件之间进行交互的一种重要方式，不仅可以指明当前组件想要执行的动作，还可以在不同组件之间传递数据。

Intent大致可以分为两种：显式Intent和隐式Intent

### 使用Intent启动活动

#### 显式Intent启动活动

1. 构建一个Intent，第一个参数为上下文，第二个参数为目标活动
2. 通过startActivity()来执行这个Intent。

```java
Intent intent = new Intent(FisrtActivity.this, SecondActivity.class);
startActivity(intent);
```

#### 隐式Intent启动活动

隐式Intent指定了一系列更为抽象的action和category等信息，然后交由系统去分析这个Intent并找出合适的活动去启动。

1. 在AndroidMenifest.xml文件中指定当前活动能够相应的action和category，只有<action>和<category>中的内容同时能够匹配Intent中指定的action和category时，这个活动才能相应Intent。
2. 使用Intent的另一个构造函数，传入action字符串，表明我们想要启动能够相应这个字符串的action的活动。如果不为Intent添加category，则会在调用startActivity()方法时添加默认category（android.intent.category.DEFAULT）到Intent中。
3. 每个Intent中只能指定一个action，但却能指定多个category。

```java
Intent intent = new Intent("com.example.activitytest.ACTION_START");
intent.addCategory("com.example.activitytest.MY_CATEGORY");
startActivity(intent);
```

隐式Intent同样可以启动其他程序的活动，如调用系统的浏览器打开网页。

```java
Intent intent = new Intent(Intent.ACTION_VIEW);

// Uri.parse() 将网址字符串解析成一个uri对象
// setDate() 传递对象
 intent.setData(Uri.parse("http://www.baidu.com"));
 
 startActivity(intent);
```
### 使用Intent传递数据

#### 向下一个活动传递数据

```java
// 第一个Activity中：将想要传递的数据暂存在Intent中
String data = "Hello, SecondActivity";
Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
intent.putExtra("key", data);

startActivity(intent);


// 第二个Activity中：根据key取出对应值
Intent intent = getIntent();
String data = intent.getStringExtra("key");
```

#### 返回数据给上一个活动

startActivityForResult()：用于启动活动，期望在新活动销毁时能返回一个结果给上一个活动。两个参数：第一个参数为Intent，第二个参数是请求码，用于在之后的回调中判断数据来源。

setResult()：专门用于向上一个活动返回数据。两个参数：1.返回处理结果（一般使用RESULT_OK或RESULT_CANCELED两个值）2.带有数据的Intent

```java
// FirstActivity中使用startActivityForResult启动新活动，并设置请求码
Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
StartActivityForResult(intent, 1);

// SecondActivity为按钮注册点击事件，添加返回数据的逻辑
Intent intent = new Intent();
intent.putExtra("data_return", "Hello, FirstActivity");

setResult(RESULT_OK, intent);
```

使用`startActivityForResult()`来启动SecondActivity，在SecondeActivity销毁后会调用上一个活动的`onActivityResult()`方法。因此需要在FirstActivity中重写改方法来获取返回的数据。

onActivityResult()方法有三个参数：

1. 启动活动时传入的请求码
2. 第二个活动返回的处理结果
3. 返回数据的Intent

```java
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // 判断数据来源
    switch (requestCode) {
        case 1:
            // 判断处理结果
            if (resultCode == RESULT_OK) {
                String data = data.getStringExtra("data_return");
                Logd.(TAG, data);
            }
            break;
        default:
    }
}
```

## 活动的生命周期

### 返回栈

Android使用**任务**来管理活动。一个任务就是一组存放在栈里的活动的集合，这个栈也称为**返回栈**。

栈是一种后进先出的数据结构。

系统总是会显示处于栈顶的活动给用户。

### 活动状态

1. 运行状态：位于栈顶时。
2. 暂停状态：不再位于栈顶，但仍然可见。
3. 停止状态：不再位于栈顶，并且完全不可见。
4. 销毁状态：从返回栈中移除后。


### 活动的生命周期

Activity类定义了**7个回调方法**，覆盖了活动生命周期的每一个环节。

- onCreate()：在活动第一次被创建的时候调用。
- onStart()：在活动由不可见变为可见时候调用。
- onResume()：在活动准备好和用户进行交互的时候调用。
- onPause()：在活动准备去启动或恢复另一个活动时候调用。
- onStop()：在活动完全不可见时调用。
- onDestroy()：在活动被销毁之前调用。
- onRestart()：在活动由停止状态变为运行状态之前调用。

### 三个生存期

1. **完整生存期**：在onCreate()和onDestroy()方法之间所经历的。一般情况下，一个活动会在onCreate()中完成各种**初始化**操作，在onDestroy()中完成**释放内存**操作。
2. **可见生存期**：在onStart()和onStop()方法之间所经历的。可以通过这两个方法，合理管理对用户可见的资源。如在onStart()中**加载资源**，在onStop()中**释放资源**，从而保证处于停止状态的活动不会占用过多内存。
3. **前台生存期**：在onResume()和onPause()方法之间所经历的。此时活动总是处于**运行状态**，可以与用户进行交互。


```java
// 当活动创建时
onCreate()
onStart()
onResume()

// 调用第二个活动
onPause()
onStop()

// 回到第一个活动
onRestart()
onStart()
onResume()

// 启动一个Dialog
onPause()

// 关闭Dialog
onResume()

// back回到桌面
onPause()
onStop()
onDestroy()
```

### 活动被回收

onSaveInstanceState()：在活动被回收之前会调用该方法，解决活动被回收时数据得不到保存的问题。

```java
// 保存临时数据
protected void onSaveInstanceState(Bundle outState) {
    super.onSaveInstanceState(outState);
    outState.putString("data_key", "data");
}

// onCreate()方法中进行数据恢复
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    
    if (savedInstanceState != null) {
        String data = savedInstanceState.getString("data_key");
    }
}
```

## 活动的启动模式

共有四种启动模式：standard、singleTop、singleTask、singleInstance。可以在AndroidManifest.xml中通过给<activity>标签指定android:launchMode属性来选择启动模式。

```xml
<activity
    android:name=".FisrtActivity"
    android:launchMode="singleTop">     <!-- 指定启动模式 -->
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android:intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

### standard

standard是活动的默认启动模式。对于使用standard模式的活动，系统不会在乎这个活动是否已经在返回栈中存在，每次启动都会创建该活动的一个新的实例。

### singleTop

在启动活动时，如果返回栈的栈顶已经是该活动，则认为可以直接使用它，不会再创建新的活动实例。

当活动并未处于栈顶位置时，再启动该活动，还是会创建新的实例的。

### singleTask

被指定为singleTask模式的活动，在整个程序的上下文中只存在一个实例。

每次启动该活动时系统首先会在返回栈中检查是否存在该活动的实例，如果发现已经存在则直接使用该实例，并把**在这个活动之上的所有活动统统出栈**，如果没有发现就会创建一个新的活动实例。

### singleInstance

被指定为singleInstance模式的活动，会启用一个新的返回栈来管理这个活动。不管哪个应用程序来访问这个活动，都共用同一个返回栈。