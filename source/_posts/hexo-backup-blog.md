---
title: Hexo 备份到 GitHub
date: 2018-09-17 12:12:00
// toc: true
tags: 
    - Hexo
    - GitHub
# description: 再也不怕电脑坏啦
---

基于 Hexo 搭建博客成功并部署到 GitHub 上后，我们会发现在 GitHub 上的文档其实只是通过 `hexo g` 生成的静态页面，并不包括我们本地的 `.md` 文件。如果这时电脑故障，就找不到博客的源文档了。所以，我们需要把一些有用的源文件也同样托管到 GitHub 上。



### 初始化 git

在博客的根目录下运行 Git Bash。

```
git init
```



### 绑定远端仓库

```
git remote add origin https://github.com/Liuluu/liuluu.github.io
```



### 提交本地文件

```
git add .
git commit -m "your description"
```



### 推送到远端新分支

```python
# git push <远程主机名> <本地分支名>:<远程分支名>
git push origin master:code
```



### 本地切换到新分支

```python
# git checkout -b <本地分支名> <远程主机名>/<远程分支名>
git checkout -b code origin/code
```



新增博客后，只要及时更新到 GitHub 上，就可以随时随地找到自己写过的内容啦。	