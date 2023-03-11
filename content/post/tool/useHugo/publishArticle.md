---
title: "Hugo 发布一篇文章的过程"
date: 2023-03-11T16:00:07+08:00
ShowToc: true
categories: 
- 工具
tags: 
- Hugo
---

## 安装
- windows：确保有 hugo.exe 在工程目录下, 并且 .gitignore 里面写上 `hugo.exe`，即可
- Mac：确保 hugo 已安装就可以


## 新建
一般，都在 post 文件夹下放 markdown 文件，使用不同文件夹来归类

> `hugo new post/tool/useHugo/publishArticle.md`

## 书写

1. 设定文章的 title, categories, tags
2. 写入内容。标题大小从 `##` 开始

## 本地预览

> `hugo server`

## 编译成 html，输出到 /docs （路径与 GitHub Action 对应）

> `hugo`

到这里，就完成了写作

## Git Push
先 fetch，再 commit，再 push。



### ~~Github Page 需要重新填写域名~~
~~因为 Github Action 在执行的时候会把 master 分支中的 /docs 内所有内容拷贝到 main 分支，这里面不包括 CNAME 文件。所以在 repository 的 setting 的 pages 重新填写域名。~~**（问题已经解决，在 workflow `action.yml` 中添加 `cname: 1-1.link`，所以如果域名有改动，需要在这修改）**
