---
title: 第一行代码 - 数据持久化
date: 2018-01-17 13:19:53
toc: true
categories:
    - Android
tags: 
    - Android
    - 第一行代码
    - 读书笔记
---

数据持久化就是指将那些内存中的瞬时数据保存到存储设备中，保证即使在手机或电脑关机的情况下，这些数据仍然不会丢失。

Android系统主要提供了3种方式用于简单地实现数据持久化功能。

- 文件存储
- SharedPreferences存储
- 数据库存储


## 文件存储

文件存储不对存储的内容进行任何的格式化处理，所有数据都是原封不动地保存到文档中。它比较适用于存储一些简单的文本数据或二进制数据。

文件存储在/data/data/<package name>/files目录下。

### 将数据存储到文件中

**方法：** openFileOutput()

**参数：**
1. 文件名
2. 文件的操作模式：
    - MODE_PRIVATE：默认模式，当文件已存在时，所写入的内容会覆盖原文件的内容。
    - MODE_APPEND：当文件已存在时，往文件里追加内容，不存在则新建。

**返回：** FileOutputSream对象。

```java
public void save() {
    String data = "Data to save";
    
    FileOutputStream out = null;
    BufferedWriter writer = null;
    
    try {
        out = openFileOutput("data", Context.MODE_PRIVATE);
        writer = new BufferedWriter(new OutputStreamWriter(out));
        
        writer.write(data);
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (writer != null) {
                writer.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 从文件中读取数据

**方法：** openFileInput()

**返回：** FileInputStream对象

```java
public String load() {
    FileInputStream in = null;
    BufferedReader reader = null;
    
    StringBuilder content = new StringBuilder;
    
    try {
        in = openFileInput("data");
        reader = new BufferedReader(new InputStreamReader(in));
        
        String line = "";
        while((line = reader.readLine()) != null) {
            content.append(line);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (reader != null) {
                reader.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    return content.toString();
}
```

## SharedPreferences存储

SharedPreferences使用键值对的方式来存储数据。

文件存储在/data/data/<package name>/shared_prefs/目录下

### 将数据存储到SharedPreferences

1. 获取SharedPreferences对象（3种方法）
    1. Context类中的getSharedPreferences()方法。
        ```java
        // 第一个参数指定SharedPreferences文件名称
        // 第二个参数指定操作模式：只有MODE_PRIVATE模式可选
        //     表示只有当前的应用程序才可以对这个文件进行读写
        pref = getSharedPreferences("data", MODE_PRIVATE);
        ```
    2. Activity类中的getPreferences()方法。
        ```java
        // 只接受一个操作模式的参数
        pref = this.getPreferences(MODE_PRIVATE);
        ```
    3. PreferencesManager类中的getDefaultSharedPreferences()方法。
        ```java
        // 接收一个Context参数，并自动使用当前程序的包名作为前缀来命名文件
        pref = PreferenceManager.getDefaultSharedPreferences(this);
        ```
2. 调用SharedPreferences对象的edit()方法来获取一个SharedPreferences.Editor对象
3. 向SharedPreferences.Editor对象中添加数据
4. 调用apply()方法将添加的数据提交

```JAVA
SharedPreferences.Editor editor = getSharedPreferences("data", MODE_PRIVATE);

editor.putString("name", "Lll");
editor.putInt("age", 18);

editor.apply();
```

### 从SharedPreferences中读取数据

1. 获取SharedPreferences对象
2. 通过get方法获取指

get方法接收两个参数，第一个参数是键，使用键去寻找对应的值；第二个参数是默认值，即当前传入的键找不到对应的值时以该默认值返回。

```java
SharedPreferences pref = getSharedPreferences("data", MODE_PRIVATE);
String name = pref.getString("name", "");
Int age = pref.getInt("age", 0);
```

## SQLite数据库存储

Android系统内置了SQLite数据库。

数据库文件会存放在/data/data/<package name>/database目录下

### SQLiteOpenHelper

为了能够更加方便地管理数据库，专门提供了一个SQLiteOpenHelper帮助类，借助这个类可以对数据库进行创建和升级。

SQLiteOpenHelper是一个抽象类，它包含：

1. 两个抽象方法：
    - onCreate()：创建数据库
    - onUpgrade()：升级数据库
2. 两个实例方法：
    - getReadableDatabase()
    - getWritableDatabase()
    - 这两个方法都可以创建或打开一个现有的数据库。不同在于：当数据库不可写入时（如磁盘空间已满），getReadableDatabase()方法返回的对象将以只读的方式去打开数据库，而getWritableDatabase()方法则会出现异常。
3. 构造方法：通常使用参数少一点的构造方法即可。
    - 第一个参数是Context
    - 第二个参数是数据库名
    - 第三个参数允许我们在查询数据的时候返回一个自定义的Cursor，一般传入null
    - 第四个参数是当前数据库的版本

构建出SQLiteOpenHelper的实例之后，再调用它的getReadableDatabase()方法或getWritableDatabase()方法就能够创建数据库了。

### 创建数据库

1. 新建MyDatabaseHelper类继承自SQLiteOpenHelper类。
    ```java
    public class MyDatabaseHelper extends SQLiteOpenHelper {
    
        public static final String CREATE_BOOK = "create table BOOK     ("
                + "id integer primary key autoincrement, "
                + "author text, "
                + "price real, "
                + "pages integer, "
                + "name text)";
                
        private Context mContext;
    
        public MyDatabaseHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version) {
            super(context, name, factory, version);
            mContext = context;
        }
    
        @Override
        public void onCreate(SQLiteDatabase sqLiteDatabase) {
            sqLiteDatabase.execSQL(CREATE_BOOK);
            Toast.makeText(mContext, "Create succeeded", Toast.LENGTH_SHORT).show();
        }
    }
    ```
2. 在MainActivity中构建出SQLiteOpenHelper的实例，调用它的getWritableDatabase()方法创建数据库。
    ```java
    MyDatabaseHelper dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
    dbHelper.getWritableDatabase();
    ```
    第一次调用getWritableDatabase()方法时会检测是否存在BookStore.db这个数据库，如果不存在则调用MyDatabaseHelper中的onCreate()方法创建数据库。


### 升级数据库

**方法：** onUpgrade()

```java
public class MyDatabaseHelper extends SQLiteOpenHelper {

    ...
    
    @Override
    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {
        sqLiteDatabase.execSQL("drop table if exists BOOK");
        sqLiteDatabase.execSQL("drop table if exists Category");

        onCreate(sqLiteDatabase);
    }
}
```

SQLiteOpenHelper的构造方法中的第四个参数是版本号，只要传入比原本大的数，就可以让onUpgrade()方法得到执行了。


### 添加数据

**方法：** insert()

**参数：** 

1. 表名
2. 未指定添加数据的情况下给某些可为空的列自动赋值null，一般用不到，可以直接传入null
3. ContentValues对象

ContentValues对象提供了一系列的put()方法重载，用于向ContentValues中添加数据。

```java
SQLiteOpenHelper db = dbHelper.getWritableDadabase();
ContentValues values = new ContentValues();

values.put("name", "Lll");
values.put("author", "Lll");
...

db.insert("Book", null, values);
values.clear;
```

### 更新数据

**方法：** update()

**参数：**

- 第一个参数：表名
- 第二个参数：ContentValues对象
- 第三、四个参数用于约束更新某一行或某几行，不指定的话默认更新所有行。

```java
SQLiteOpenHelper db = dbHelper.getWritableDatabase();
ContentValues values = new ContentValues();

values.put("price", 10.99);

db.update("Book", values, "name = ?", new String[] {"Lll"});
```

第三个参数对应的是SQL语句的where部分，表示更新所有name等于?的行，而?则是一个占位符，可以通过第四个参数提供的字符串数组为第三个参数中的每个占位符指定相应的内容。

### 删除数据

**方法：** delete()

**参数：** 

- 第一个参数：表名
- 第二、三个参数用于约束删除某一行或某几行

```java
SQLiteOpenHelper db = dbHelper.getWritableDatabase();
db.delete("Book", "pages > ?", new String[] {"500"});
```

### 查询数据

**方法：** query()

query()方法的参数非常复杂，最短的一个方法重载也需要**7个参数**：

- 第一个参数是表名
- 第二个参数用于指定查询哪几列
- 第三、四个用于约束查询条件，如果不指定则默认查询所有行
- 第五个参数用于指定去group by的列
- 第六个参数用于对group by之后的数据进行进一步过滤
- 第七个参数用于指定查询结果的排序方式

query()方法返回一个**Cursor对象**，Cursor对象包含以下几种常用方法：

- moveToFirst()：将数据的指针移动到第一行的位置
- getColumnIndex()：获取到某一列在表中对应位置的索引，将索引传到对应的取值方法中，就，而可以得到从数据库中读取到的数据了
    ```java
    int pages = cursor.getInt(cursor.getColumnIndex("pages"));
    ```

### 使用SQL操作数据库

#### 添加、更新、删除数据

方法：db.execSQL();

```java
db.execSQL("update Book set price = ? where name = ?", new String[] {"10.99", "Lll"});
```


#### 查询数据

方法：db.rawQuery();

```java
db.rawQuery("select * from Book", null);
```