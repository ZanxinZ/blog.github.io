---
title: "Git 分支管理"
date: 2025-08-31T04:39:07+08:00
ShowToc: true
categories: 
- Develop
tags: 
- Git
---

### 较为标准的版本（多人协作）

master 分支：用于存放稳定的生产环境代码，每个合并到master的提交通常都对应一个发布版本。

develop 分支：用于源代码功能集成

feature分支：基于develop 分支，拉出新分支来开发，开发过程中，需要经常切回develop分支pull最新更改（可能来自其他同事），然后切回feature分支做 git merge develop；开发完成之后提起 Merge Request （也叫 Pull Request）

release1.1.0分支：要发布时，基于 develop 分支来拉出

当develop分支达到稳定状态时，会通过release分支将代码合并到master分支。（放到 master 上即代表发包完成，并且可以从master上拿这个包去发布给官方平台）

bug 修复：

- develop 阶段：在develop分支或相关feature 分支上修复
- release 阶段，则基于 release 拉出 hotfix分支上修复bug，修复后合入 release 并且事后将修改同步到 develop 分支。
- 如果已经上线（已经由 release 提交到master中），则基于 master 分支的最新 release 去拉出hotfix分支上修复bug，然后合并回 master 和 develop。

master(main)分支的主要作用是：

- 代表生产环境的代码，包含已经部署到生产环境的稳定版本
- 是项目的官方发布历史，每个合并到master的提交通常都对应一个发布版本
- 需要保持高度稳定，确保任何时候都可以从这个分支部署到生产环境
- 通常设有保护措施，不允许直接提交，而是通过合并release或hotfix分支的方式更新

在Git流工作流中，master分支与develop分支共同工作：

- develop分支用于集成开发中的功能
- release 代表产品的不同版本
- 当develop分支达到稳定状态时，会通过release分支将代码合并到master分支
- 对于生产环境中发现的紧急问题，会从master分支创建hotfix分支进行修复，然后同时合并回master和develop分支

### 非标准版本（两三个人的协作时候可以）

develop 分支作为源代码管理

feature 分支开发功能，完成后合入 develop 分支

产品版本各个功能开发完成后，直接将 develop 分支的代码拿去打包提交审核。

产品版本节点使用 tag 标记。例如 v1.1.2。







