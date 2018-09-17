---
title: Eclipse - 注释配置
date: 2017-08-02 13:14:20
// toc: true
tags: 
    - eclipse
    - java
description: 在Eclipse中快速自动生成注释
---


## 配置注释模板

路径：window -> Preference -> Java -> Code Style -> Code templates

![][3]

- Comments：
	- Files：java文件注释
	- Types：java类注释
	- Fields：类字段注释
	- Constructors：构造函数注释
	- Methods：方法注释
	- Overriding methods：重写方法注释
	- Delegate methods：代理方法注释
	- Getters：Getter方法注释
	- Setters：Setters方法注释
- Code：
	- New Java files（新建java文件代码模板）  
	- Method body（方法体模板）
	- Constructor body（构造函数模板）
	- Getter body（字段Getter方法模板）
	- Setter body（字段Setter方法模板）
	- Catch block body（异常catch代码块模板）  


## 配置注释模板2@changes

路径：Window -> Preferences -> Java -> Editor -> Templates -> New

![][1]

使用：

![][2]

## 快捷键调用注释模板

默认快捷键：alt+shift+j
选中方法名（或者其他要注释的的内容）后，按快捷键。

![][4]

### 修改默认快捷键

路径：Window -> Preferences -> General -> Keys;

![][5]

  [1]: /img/eclipse/1.jpg
  [2]: /img/eclipse/2.jpg
  [3]: /img/eclipse/3.jpg
  [4]: /img/eclipse/4.jpg
  [5]: /img/eclipse/5.jpg
  
 