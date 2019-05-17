---
title: DOS 批处理 + adb 实现批量重装启动 app
date: 2018-11-7 22:26:03
// toc: true
categories:
    - 测试
tags: 
    - 测试
    - DOS批处理
description: 你已经是个成年人了，要学会自己工作赚钱养活自己了。
---



## 背景

项目测试时有个需求需要实现批量重装 app 并启动。首先想到的是通过 adb 命令实现重装和启动 app。

启动 app：

```
adb shell am start -n packageName/packageName.activityName
```

清除 app 数据，模拟重装：

```
adb shell pm clear packageName
```



而每次都去手动执行两个命令，显然是一件浪费时间又毫无意义的事情。这个时候就可以通过 Dos 批处理命令让他自动的去执行命令。



> 批处理：**批处理**(Batch)，也称为批处理脚本。顾名思义，批处理就是对某对象进行批量的处理，通常被认为是一种简化的脚本语言，它应用于DOS和Windows系统中。批处理文件的扩展名为bat 。 
>
> 
>
> DOS批处理则是基于DOS命令的，用来自动地批量地执行DOS命令以实现特定操作的脚本。更复杂的情况，需要使用if、for、goto等命令控制程式的运行过程，如同C、Basic等高级语言一样。如果需要实现更复杂的应用，利用外部程式是必要的，这包括系统本身提供的外部命令和第三方提供的工具或者软件。批处理程序虽然是在命令行环境中运行，但不仅仅能使用命令行软件，任何当前系统下可运行的程序都可以放在批处理文件中运行。 



## 实现

循环执行启动 app 和清除 app 缓存命令。定义变量 n ，每次循环使 n 的值加 1，当 n 达到指定数量后退出循环：

1. 定义变量 n
2. 设置标志位 start
3. n = n +1
4. 当 n = 200 时，停止执行
5. 启动 app 的指定 Activity
6. 等待 5s
7. 清除 app 缓存
8. 跳转到标志位 start

```
set n=0
:start
set /a n+=1
if %n%==200 pause
adb shell am start -n com.xxx.xxx/com.xxx.xxx.MainActivity
timeout /t 5
adb shell pm clear com.xxx.xxx
goto start
```



## Dos 批处理常用逻辑

### 条件判断

**关键字**：if

**完整格式**：`if 条件表达式 (语句1) else (语句2)`



eg. 如果 D 盘下存在 test.txt 文件，则输出“D盘下有test.txt存在”，否则输出“D盘下不存在test.txt”。

```
if exist d:\test.txt (echo D盘下有test.txt存在) else (echo D盘下不存在test.txt) 
```



#### 与

与命令有两种写法：

```
if 1==1 if 2==2 echo Ok
```

第一种写法相对比较简单，而第二种写法则比较清晰：

```
if 1==1 (if 2==2 echo Ok)
```

第二种写法相当于：

```java
if(1==1){
    if(2==2){
         System.out.println("Ok");	// echo ok
    }
}
```



#### 或

或命令的实现方式很多，这里使用 GOTO 语句实现或：只要满足 1 个条件，就直接调用满足条件的语句。

```
if 1==1 goto start
if 2==2 goto start

:start
echo hello
```

但这个实现有个问题，如果 if 的执行语句不是子程序，而是正常语句，则会造成重复输出。如：

```
if 1==1 echo a
if 2==2 echo a
```

则此时会输出两个 a，跟或命令期望结果不符。

该问题可通过 if 嵌套的方式来解决：

```
if 1==1 (echo a) else (if 2==2 (echo a))
```



#### 非

在条件判断前加 not，即可实现非判断。

```
if not 1==2 echo ok
```



### 循环

Dos 命令实现循环可以通过 for 命令和 goto 命令来实现。

#### for

对一组文件、字符串或命令结果中每个对象运行指定的命令。

**格式**：for [参数] %变量名 in (相关文件或命令) do 执行的命令 

**注意**：在批处理中使用，则变量前的 `%` 需要改成 `%%`

eg. 循环输出1,2,3

```
for %i in (1,2,3) do echo %i
```

#### goto

goto 实现循环则要配合其他命令来使用，它本身只能起到强制跳到某行执行的功能。如上述例子中，goto 只起到跳到某个标志位开始执行，实际通过变量 n 和 pause 命令控制循环的跳出。

```
set n=0
:start
set /a n+=1
if %n%==200 pause
echo %n%
goto start
```



## 参考文档

1. [cmd if条件 条件判断](https://www.jb51.net/article/18979.htm)
2. [DOS命令 的条件判断（与或非：and、or 、not）](https://blog.csdn.net/hongweigg/article/details/60868917)
3. [终极dos批处理for循环命令详解](https://www.jb51.net/article/93170.htm)