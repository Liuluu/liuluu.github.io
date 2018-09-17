---
title: Hello World!
date: 2017-07-19 22:17:42
// toc: true
tags: 
    - Hexo
    - github
description: 纪念寄几第一次搭建博客成功 =w=
---

　　因为[江佳扬](http://jalan.space)同学这两天一直在我旁边鼓捣博客迁移，搞得我也心痒痒的想要搭一个个人博客。说干就干的我就这么自己照着网上的教程成功搭成了人生中的第一个博客。博客采用的是`Hexo`框架，搭建在`github`上。以下记录下本~~制杖~~仙女搭建博客的全过程。


##  配置Hexo

### 安装Hexo

安装Hexo首先要安装以下程序：

- Node.js
- Git

安装完成后，打开Git-bash，在命令行输入以下命令即可完成Hexo的安装：
```
npm install -g hexo-cli

//可输入以下代码查看版本，有版本号代表安装成功。
hexo -v
```

### 配置Hexo

在本地创建一个文件夹，如D:/hexo。打开Git-bash，依次输入以下命令进行初始化：
```
hexo init D:/hexo
cd D:/hexo
npm install
```
初始化完成后，输入：
```
hexo generate   //生成静态页面
hexo server     //启动本地服务
```
此时会启动本地部署好默认的博客网站，打开浏览器输入：http://localhost:4000/ ，就可以看到搭建在本地上的博客啦。=w=

## 配置Github

### 创建Github帐号

创建一个Github帐号，如果是首次创建的话记得要到邮箱进行Github帐号的认证哈。帐号认证通过后要创建一个同名的仓库，如帐号是myaccount，新建的仓库名就是myaccount.github.io。

### 配置SSH

#### 1. 生成SSH

检查是否已经有SSH Key，打开Git-bash，输入
```
cd ~/.ssh
```
如果没有这个目录的话，就生成一个新的SSH，输入
```
ssh-keygen -t rsa -C "e-mail"   // e-mail处填写你注册Github时的邮箱
```
接下来疯狂回车，当前步骤完成OvO。

#### 2. 配置公钥

打开~/.ssh/id_rsa.pub文件（找不到的话可以看生成步骤，会显示生成文件所在路径），复制里面的全部内容。

打开Github，登录后点击右上角头像 -> Settings，在个人设置页面左侧找到SSH and GPG keys。新建一个SSH keys，随意输入一个title，再将之前复制好的内容粘贴到key输入框中，点击Add key即可。

检查SSH是否配置成功，在命令行输入：
```
ssh -T git@github.com
```
如果显示以下内容，则说明SSH配置成功。
```
Hi username! You've successfully authenticated, but GitHub does not
provide shell access.
```
跟着教程走的时候，我这里还出现了其他奇怪的内容，但是并不影响SSH配置成功，于是就忽略它了。
#### 3. 配置Github帐户

在命令行输入：
```
git config --global user.name "username"
git config --global user.email "email"   //这里的username和email替换成注册git时的对应信息
```

配置完后，输入：
```
git config --list   //查看已设配置
```
检查username和email是否正确。

打开D:/hexo文件下中的_config.yml文件，找到如下位置，填写：
```
deploy: 
    type: git
    repo: git@github.com:Myaccount/Myaccount.github.io //Myaccount替换成你的git用户名
```

注意：yml文件中的`:`后面要带一个空格！

## 更新Github

在Git-bash中进入网站根目录：
```java
cd D:/hexo
hexo g     //生成静态页面
hexo d     //部署到github
```

这里如果报错的话可以检查一下是否未安装hexo-deployer-git插件。未安装则执行以下语句：
```
npm install hexo-deployer-git --save
```

更新成功之后，就可以访问 **你的git用户名.github.io** 来查看博客啦=w=。

## 更换博客主题

这时候已经可以通过Myaccount.github.io来访问博客啦，但博客显示的还是Hexo的默认主题。通过[Themes|Hexo](https://hexo.io/themes/)挑选自己喜欢的主题后，就可以着手给自己的博客更换主题啦。

通用的更换博客主题的步骤如下：

#### 1. 下载主题

- 通过git链接直接复制到本地：
```java
git clone url themes/themesname  //url为主题的git链接，themesname为主题名
```
- 直接下载主题文件到博客的根目录的themes文件夹下。

#### 2. 配置主题

打开博客根目录下的_config.yml文件，本例子为D:/hexo/_config.yml。找到theme字段，并将它的值改为你的主题名（注：要和你theme文件夹内的主题名一致）。
```
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: maupassant
```

#### 3. 重新生成并部署到Github

配置好主题后，可以重新生成并在本地验证是否正常。
```java
cd D:/hexo  //进入博客的根目录
hexo g      //重新生成静态页面
hexo s      //启动本地服务器
```
本地验证没有问题的话就可以部署到git上啦。
```java
hexo d      //上传到git
```

通用的主题配置步骤就是以上三步啦，但不同的主题可能有自己的要求，比如maupassant主题就还需要下载两个插件。安装的时候，可以看一下自己选择的主题有没有什么特殊~~需~~要求。

## 其他

### 发布文章

发布文章可以用命令的形式：
```
hexo new "name"     //新建一个name.md在D:/hexo/source/_post文件夹下
```

当然，更直接的做法是在根目录的source/_post文件夹下直接创建一个.md文件。书写文章需要使用markdown语法。

### 常用命令


```
hexo clean      //清除缓存文件和已生成的静态文件
hexo generate   //生成网站静态文件，同hexo g
hexo server     //启动本地服务器，预览主题。同hexo s
hexo deploy     //自动生成网站静态文件，并部署到设定好的git仓库上，同hexo d
```

## 参考资料

- [Github+Hexo+NEXT主题+域名绑定 博客搭建全记录](http://barrysite.me/2017/05/07/Github%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/)
- [大道至简——Hexo简洁主题推荐](https://www.haomwei.com/technology/maupassant-hexo.html)