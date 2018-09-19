---
title: 第一行代码 - 碎片
date: 2018-01-13 19:19:53
toc: true
categories:
    - Android
tags: 
    - Android
    - 第一行代码
    - 读书笔记
description: 兼容界的小能手 Fragment
---

碎片（Fragment）是一种可以嵌入在活动当中的UI片段。

碎片的使用分为两种：静态添加碎片和动态添加。

### 静态添加

1. 新建两个碎片布局文件：left_fragment.xml和right_fragment.xml
2. 新建两个碎片类，继承自`Fragment`。这里建议继承自support-v4库中的Fragment，可以让碎片在所有的Android系统版本中保持功能一致性。

```java
public class LeftFragment extends Fragment {
    @Override
    public view onCreateView(LayoutInflater inflater, ViewGroup container,
        Bundle savedInstanceState) {
        // 通过inflater的inflate方法将left_fragment布局动态加载
        View view = inflater.inflate(R.layout.left_fragment, container, false);
        return view;
    }
}
```
3. 修改activity_main.xml文件，使用`<fragment>`标签在布局中添加碎片。这里需要通过`android:name`属性来显示指明要添加的碎片类名。

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    
    <fragment
        android:id="@+id/left_fragment"
        android:name="com.example.lll.fragmenttest.LeftFragment"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />
        
    <!-- 另一个碎片同上添加 -->
</LinearLayout>
```

### 动态添加

1. 将activity_main.xml中的右侧碎片替换成一个Fragment。

```xml
<Fragment
    android:id="@+id/right_layout"
    android:layout_width="0dp"
    android:layout_height="match_parent"
    android:layout_weight="1" />
```

2. 动态添加碎片：
    1. 创建待添加的碎片实例
    2. 获取FragmentManager：在活动中可以直接通过调用`getSupportFragmentManager()`方法得到
    3. 开启一个事务，通过调用`beginTransaction()`方法开启。
    4. 向容器内添加或替换碎片，一般使用`replace()`方法实现，需要传入容器的id和待添加的碎片实例。
    5. 提交事务，调用`commit()`方法。

```java
private void replaceFragment(Fragment fragment) {

    FragmentManager fragmentManager = getSupportFragmentManager();
    FragmentTransaction transaction = fragmentManager.beginTransaction();
    
    transaction.replace(R.id.right_layout, fragment);
    transaction.addToBackStack(null);   // 模拟返回栈
    
    transaction.commit();
}
```

### 在碎片中模拟返回栈

addToBackStack()方法，可以用于将一个事务添加到返回栈中。

它可以接收一个名字用于描述返回栈的状态，一般传入null即可。

### 碎片和活动之间通信

1. 获取碎片的实例：FragmentManager提供了一个`findFragmentById()`方法用于从布局文件中获取碎片的实例

```java
RightFragment rightFragment = (RightFragment) getSupportFragmentManager.findFragmentById(R.id.right_fragment.xml);
```

2. 在碎片中通过调用`getAsctivity()`方法来得到和当前碎片相关联的活动实例。

```java
MainActivity activity = (MainActivity) getActivity();
```

### 碎片的生命周期

碎片附加的回调方法：

- onAttach()：当碎片和活动建立关联时调用。
- onCreateView()：为碎片创建视图（加载布局）时调用。
- onActivityCreated()：确保与碎片相关联的活动一定已经创建完毕的时候调用。
- onDestroyView()：当与碎片关联的视图被移除的时候调用。
- onDetach()：当碎片与活动解除关联的时候调用。


```java
addFragment
onAttach
onCreateView
onActivityCreated
onStart
onResume

onPause
onStop
onDestroyView
    onDestroy
    or
    碎片已添加到返回栈则->onCreateView
onDetach
```