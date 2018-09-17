---
title: Android笔记 - 广播
date: 2018-01-13 21:19:53
toc: true
categories:
    - Android
tags: 
    - Android
    - 第一行代码
    - 读书笔记
description: 发送爱你的消息 (๑•ᴗ•๑)
---

### 简介

- 发送广播：Intent
- 接收广播：广播接收器（Broadcast Receiver）

**两种类型：**

- 标准广播：异步执行，所有的广播接收器几乎都会在同一时刻接收到这条广播消息。
- 有序广播：同步执行，同一时刻只会有一个广播接收器能够收到这条广播消息。此时广播接收器有先后顺序，优先级高的广播接收器会先收到广播，甚至可以截断广播。


## 接收广播

### 创建广播接收器

新建一个类，继承自BroadcastReceiver类，并重写父类中的`onReceiver()`方法。

当有广播到来时，onReceiver()方法就会得到执行。

```java
class NetworkChangeReceiver extends BroadcastReceiver {
    @Override
    public void onReceiver(Context context, Intent intent) {
        //...
    }
}
```

**注意**：不要在`onReceive()`方法中添加过多的逻辑或者进行任何耗时的操作，当该方法运行了较长时间而没有结束时，程序就会报错。

### 注册广播

1. 动态注册：在代码中注册
2. 静态注册：在AndroidManifest.xml中注册

#### 动态注册

1. 创建一个IntentFilter实例，并添加要监听的action
2. 创建一个广播接收器的实例
3. 注册广播监听器：registerReceiver()。

```java
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE"); //系统网络变化监听

NetworkChangeReceiver receiver = new NetworkChangeReceiver();

registerReceiver(receiver, intentFilter);
```

**注意：** 动态注册的广播接收器一定要取消注册。使用`unregisterReceiver()`方法取消注册。

```java
protected void onDestroy() {
    super.onDestroy();
    unregisterReceiver(receiver);
}
```

**优缺点：** 可以自由地控制注册与注销，但必须在程序启动之后才能接受到广播。

#### 静态注册

1. 新建一个广播接收器：右击包 -> New -> Other -> Broadcast Receiver
2. 在AndroidManifest.xml文件中注册

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.lll.databasetest">
    
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        ...
        <receiver
            android:name=".BootCompleteReceiver"
            android:enabled="true"
            android:exported="true" >
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
            </intent-filter>
        <receiver>
    </application>
</manifest>
```

## 发送自定义广播

### 发送标准广播

调用Context的sendBroadcast()方法将广播发送出去。

```java
Intent intent = new Intent("com.example.lll.broadcasttest.MY_BROADCAST");
sendBroadcast(intent);
```

### 发送有序广播

调用Context的sendOrderedBroadcast()方法将广播发送出去。

该方法接收两个参数：1.Intent；2.与权限相关的字符，一般传null。

```java
Intent intent = new Intent("com.example.lll.broadcasttest.MY_BROADCAST");
sendOrderedBroadcast(intent);
```

## 使用本地广播

本地广播机制：发出的广播只能在应用程序的内部进行传递，并且广播接收器也只能接收来自本应用程序发出的广播。

主要是使用了一个LocalBroadcastManager来对广播进行管理。

- 获取LocalBroadcastManager的实例：通过它的静态方法getInstance()
- 注册广播接收器：LocalBroadcastManager的sendBroadcast()方法
- 发送广播：LocalBroadcastManager的registerReceiver()方法

**注意：** 本地广播无法通过静态注册的方式来接收。

```java
public class MainActivity extends AppCompatActivity {
    private IntentFilter intentFilter;
    private LocalReceiver localReceiver;
    private LocalBroadcastManager localBroadcastManager;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        //...
        localBroadcastManager = LocalBroadcastManager.getInstance(this);
        
        // 发送本地广播
        Intent intent = new Intent("com.example.broadcasttest.LOCAL_BROADCAST");
        localBroadcastManager.sendBroadcast(intent);
        
        // 接收本地广播
        intentFilter = new IntentFilter("com.example.broadcasttest.LOCAL_BROADCAST");
        localReceiver = new LocalReceiver();
        
        localBroadcastManager.registerReceiver(localReceiver, intentFilter);
    }
    
    @Override
    protected void onDestroy() {
        super.onDestroy();
        localBroadcastManager.unregisterReceiver(localReceiver);
    }
}
```

## 其他

#### 为广播接收器设置优先级

注册时通过android:priority属性给广播接收器设置优先级。

```xml
<receiver
        android:name=".BootCompleteReceiver"
        android:enabled="true"
        android:exported="true" >
        <intent-filter android:priority="100">
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
<receiver>
```

#### 截断广播

调用`abortBroadcast()`方法，这条广播截断，后面的广播接收器将无法再接收到这条广播。
