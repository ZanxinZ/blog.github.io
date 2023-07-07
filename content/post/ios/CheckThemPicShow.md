---
title: "“小速记” App 介绍"
date: 2023-07-07T13:12:14+08:00
ShowToc: true
categories: 
- 软件开发
tags: 
- iOS
---

### 《小速记》各部分功能介绍

1. 主页面，各个功能入口

    ![](../CheckThemPickShow/1.png)

2. ToDo 功能
    
    新建 Todo 时，可设定 Todo 的表情 emoji 、时间段。
    右滑删除，左滑完成 Todo，长按 Todo 可设定一个在几分钟后的系统通知推送（提醒这个 Todo）

    ![](../CheckThemPickShow//2.png)

3. 每日打卡

    可以新建任务，设定为每日打卡、每周打卡或每月打卡。
    打卡任务有进度，任务完成后会将任务归档。

    ![](../CheckThemPickShow/4.png)

    ![](../CheckThemPickShow/3.png)

4. 数字记录器

    可以为某一件事情添加计数器，用于腐竹记忆生活中的琐碎数字。

    ![](../CheckThemPickShow//5.png)

5. 数据

    Todo、每日打卡、数字记录器是都作为任务，使用 CoreData 存放于本地。
    对应的，有 ArchivedTodo、ArchivedDailyTask、ArchivedRecord 作为归档对象，在任务完成后作为记录存放于本地。

6. 演示

    [App 使用视频演示](https://www.bilibili.com/video/BV1Vh4y1E7Gx/?vd_source=c24c919e207e47d8f84bb5082e08de26)