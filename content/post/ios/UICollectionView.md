---
title: "UICollectionView"
date: 2025-01-10T16:01:03+08:00
ShowToc: true
categories: 
tags: 
- iOS
---

注册cell 的时候，是 XX 类型和 YY identifier。
一个 XX 可以对应多个 YY。

```swift
collectionView.register(MyFirstCell.self, forCellWithReuseIdentifier: "FirstCell")
collectionView.register(MySecondCell.self, forCellWithReuseIdentifier: "SecondCell")
```

应用场景：同一个 UICollectionView 可以有多种 cell，例如有一个额外的带有 ➕ 的 cell，用于向 UICollectionView 添加新的元素。

