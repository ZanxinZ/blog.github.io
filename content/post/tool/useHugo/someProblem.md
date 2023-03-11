---
title: "Hugo 的一些问题"
date: 2023-03-11T16:59:18+08:00
ShowToc: true
categories: 
- 工具
tags: 
- Hugo
---

## 若域名有变动

需要修改三个地方

- workflow 文件夹里 Github Acction 配置文件 `Action.yml` 中的 `cname:`
- config.yaml 中的 `baseURL`
- Github repository setting `Pages`

