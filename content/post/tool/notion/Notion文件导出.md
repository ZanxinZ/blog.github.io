---
title: "Notion文件导出"
date: 2023-03-11T15:11:32+08:00
ShowToc: true
categories: 
- 工具 
tags: 
- Notion
---

## Notion 批量文件导出，以 PFD格式

Notion 是一款 markdown 笔记软件，可以快速书写，多端同步，支持文件导出，十分方便。
我在 notion 中写了很多页面，有时要转移到别的地方保存，那么应该怎么做呢？

### notion 支持导出的文件格式：PDF，HTML，MD

![Untitled](../Notion文件导出/Untitled.png)

可是，这几种方法都有缺点。

- PDF：只能当前页面，导出所有子页面，需要升级 Pro
- HTML：多出了一些附带的文件，文件散乱，转移和浏览都不方便
- MD：导出之后，图片和文本都分开，转移不方便，文件散乱

### 那么，有没有更简单的办法获取我自己写的许多页面，且保存为 PDF ？

有的，步骤如下：

1. 在notion中包含子页面导出 markdown
2. 使用 vs code 打开，使用插件 Markdown PDF 逐页导出。

### 具体操作步骤

下图可以看到我的一个页面包含了多个子页面。

![Untitled](../Notion文件导出/Untitled1.png)

右上角三个点的按钮，选择 export

![Untitled](../Notion文件导出/Untitled2.png)

1. 导出 markdown 
    
    ![Untitled](../Notion文件导出/Untitled3.png)
    
2. 会得到页面和子页面的目录结构

![Untitled](../Notion文件导出/Untitled4.png)

![Untitled](../Notion文件导出/Untitled5.png)

1. 使用 vscode 打开。
2. 安装插件 ”Markdown PDF“
    
    ![Untitled](../Notion文件导出/Untitled6.png)
    
    搜索 Markdown PDF， 点击安装 install。
    
    ![Untitled](../Notion文件导出/Untitled7.png)
    
3. 设置 ”auto convert when save “
    
    在插件库里可以看到已安装 Markdown PDF
    
    ![Untitled](../Notion文件导出/Untitled8.png)
    
    ![Untitled](../Notion文件导出/Untitled9.png)
    
    这样一来，打开一个 md 文件，ctrl + s，它就自动转换为 pdf 并输出到源路径了。
    
    当然，也可以自己设定输出的路径，方便批量管理。
    
    ![Untitled](../Notion文件导出/Untitled10.png)
    
    ![Untitled](../Notion文件导出/Untitled11.png)
    

### 总结

到这里，就实现了 Notion 写作内容的固化和转移啦！