---
title: Android笔记 - 运行时权限
date: 2018-01-23 10:19:53
toc: true
categories:
    - Android
tags: 
    - Android
    - 第一行代码
    - 读书笔记
description: 注意：收到一个爱你的申请。
---

## 权限机制

### 权限声明

在AndroidManifest.xml文件中进行权限申明：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.lll.litepaltest">
    
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ...
</manifest>
```

加入权限申明后，用户可以在以下两个方面得到保护：

1. 用户在低于6.0系统的设备上安装，会在安装时提醒应用所需要的权限
2. 用户可以随时在应用程序管理界面查看任意一个程序的权限申请情况

Android在6.0系统上增加了**运行时权限**功能。用户不需要在安装软件的时候一次性授权所有申请的权限，而是可以在软件的使用过程中对某一项权限申请进行授权。即使拒绝了某个权限申请，仍然可以继续使用这个应用的其他功能。

### 权限分类

1. 普通权限：不会直接威胁到用户的安全和隐私权限。对于这部分权限申请，系统会自动帮我们进行授权。
2. 危险权限：可能会触及用户隐私或者对设备安全性造成影响的权限，必须由用户手动点击授权。

对于危险权限，我们需要进行运行时权限处理。

**注意：** 在进行运行时权限处理时使用的是某个权限名，但是用户一旦同意授权，该权限组中所有的其他权限也会同时被授权。


## 在程序运行时申请权限

1. 定义一个按钮，当点击按钮时就去触发拨打电话的逻辑
2. 修改AndroidManifest.xml文件，声明如下权限：
    ```xml
    <uses-permission android:name="android.permission.CALL_PHONE" />
    ```
3. 进行运行时权限处理
    1. 判断用户是否已授权过：`ContextCompat.checkSelfPermission()`。接受两个参数：1.Context；2.权限名
    2. 如果未授权则调用`ActivityCompat.requestPermissions()`方法来向用户申请授权。接受三个参数：1.Acitivity实例；2.权限名数组(String)；3.请求码。
    3. 调用了申请授权方法后会回调到`onRequestPermissionsResult()`方法中。授权的结果会封装在grantResults参数中。

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button makeCall = findViewById(R.id.make_call);
        makeCall.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (ContextCompat.checkSelfPermission(MainActivity.this,
                        Manifest.permission.CALL_PHONE) != PackageManager.PERMISSION_GRANTED) {
                    ActivityCompat.requestPermissions(MainActivity.this,
                            new String[] {Manifest.permission.CALL_PHONE}, 1);
                } else {
                    call();
                }
            }
        });
    }

    private void call() {
        try {
            Intent intent = new Intent(Intent.ACTION_CALL);
            intent.setData(Uri.parse("tel:10086"));

            startActivity(intent);
        } catch (SecurityException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        switch (requestCode) {
            case 1:
                if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    call();
                } else {
                    Toast.makeText(this, "denied permission", Toast.LENGTH_SHORT).show();
                }
                break;
            default:
        }
    }
}
```