---
title: "如何通过点击 UICollectionViewCell 跳转至对应的 UIViewController"
date: 2023-05-17T22:41:55+08:00
ShowToc: true
categories: 
tags: 
---


## 跳转部分的实现

我需要从我的 HomeViewController 通过点击不同的 CollectionViewCell 跳至不同的 ViewController 

![Untitled](../%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E7%82%B9%E5%87%BB%20UICollectionViewCell%20%E8%B7%B3%E8%BD%AC%E8%87%B3%E5%AF%B9%E5%BA%94%E7%9A%84%20UIViewController/Untitled.png)

![Untitled](../%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E7%82%B9%E5%87%BB%20UICollectionViewCell%20%E8%B7%B3%E8%BD%AC%E8%87%B3%E5%AF%B9%E5%BA%94%E7%9A%84%20UIViewController/Untitled%201.png)

思路：

1. 首先是需要把目标 ViewController 存放起来，在点击 cell 时可以作为目的地进行 present 跳转。

因为我的多个不同的 ViewController 都继承自 UIViewController， 那么我想用它作为父类型存放在cell中。

![Untitled](../%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E7%82%B9%E5%87%BB%20UICollectionViewCell%20%E8%B7%B3%E8%BD%AC%E8%87%B3%E5%AF%B9%E5%BA%94%E7%9A%84%20UIViewController/Untitled%202.png)

1. 先把 Main storyboard 存为当前类的属性，以方便初始化各个 collectionView

![Untitled](../%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E7%82%B9%E5%87%BB%20UICollectionViewCell%20%E8%B7%B3%E8%BD%AC%E8%87%B3%E5%AF%B9%E5%BA%94%E7%9A%84%20UIViewController/Untitled%203.png)

1. 然后使用 storyboard 自带的动态反射方法 instantiateViewController，通过字符串找到对应的 ViewController

![Untitled](../%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E7%82%B9%E5%87%BB%20UICollectionViewCell%20%E8%B7%B3%E8%BD%AC%E8%87%B3%E5%AF%B9%E5%BA%94%E7%9A%84%20UIViewController/Untitled%204.png)

1. 在 dataSource 的实现中，将 cell 的属性绑定为对应的 controllerView 目标。

![Untitled](../%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E7%82%B9%E5%87%BB%20UICollectionViewCell%20%E8%B7%B3%E8%BD%AC%E8%87%B3%E5%AF%B9%E5%BA%94%E7%9A%84%20UIViewController/Untitled%205.png)

1. 最后是 didTapCell 方法，是点击后的具体要做的动作，即跳转。这里的 target 类型是 UIController。

![Untitled](../%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E7%82%B9%E5%87%BB%20UICollectionViewCell%20%E8%B7%B3%E8%BD%AC%E8%87%B3%E5%AF%B9%E5%BA%94%E7%9A%84%20UIViewController/Untitled%206.png)

发现这样实现不了，原因是第 1 步中 MainChoiceCell 中的 targetController 不能是 weak，若为 weak 那么它在被赋值然后在函数结束时（closure 结束）会释放引用也就是恢复为 nil，所以正确的做法应该是把 weak 去掉。

![Untitled](../%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E7%82%B9%E5%87%BB%20UICollectionViewCell%20%E8%B7%B3%E8%BD%AC%E8%87%B3%E5%AF%B9%E5%BA%94%E7%9A%84%20UIViewController/Untitled%207.png)

## 手指触碰 UICollectionViewCell 但未释放，这属于 Highlight

UICollectionViewCell 底层来自 UIView。重写 highlight 的 willSet， 手指点在 UICollectionViewCell 区域会触发 highlight 置为 true ；手指不松开，移动到不属于 UICollectionViewCell 的区域，则会触发 highlight 置为 false。

```swift
override var isHighlighted: Bool {
        willSet {
            if newValue {
                UIView.animate(withDuration: 0.2, animations: {
                    self.transform = CGAffineTransform(scaleX: 1.1, y: 1.1)
                })
            } else {
                UIView.animate(withDuration: 0.2, animations: {
                    self.transform = CGAffineTransform(scaleX: 1, y: 1)
                })
            }
           
        }
    }
```

实现了进入时扩大，离开时缩小，与 UITapGestureRecognizer 互相独立。使用 UITapGestureRecognizer 实现点击监听SwiftCopyCaption

```swift
cell.addGestureRecognizer(UITapGestureRecognizer(target: self, action: #selector(didTapCell(_:))))
```

对应的触发任务

```swift
@objc func didTapCell(_ gesture: UITapGestureRecognizer) {
        guard let cell = gesture.view as? MainChoiceCell else { return }
        
            UIView.animate(withDuration: 0.2, delay: 0.2, animations: {
                cell.transform = CGAffineTransform(scaleX: 1, y: 1)
            })
            
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.2, execute: {
                if let target = cell.targetController{
                    
                    self.present(target, animated: true)
                    
                }
            })
    }
```

### 使用 UITapGestureRecognizer 存在问题

点 cell 然后不松开，手指移到 cell 外边再移回来，它的 isHighlight 会再次被触发（正常），但此时 UITapGestureRecognizer  所发出的事件已经失效，即使此时松开手指，也不会执行触发任务（跳转到另一个 ViewController）。

### 使用 didSelectedItemAt 可以解决问题

在 UICollectionView 的 delegate 中实现函数

```swift
func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath) {
        let cell = collectionView.cellForItem(at: indexPath) as! MainChoiceCell
        UIView.animate(withDuration: 0.2, delay: 0.2, animations: {
            cell.transform = CGAffineTransform(scaleX: 1, y: 1)
        })
        
        DispatchQueue.main.asyncAfter(deadline: .now() + 0.2, execute: {
            if let target = cell.targetController{
                // 需要执行的动作
                self.present(target, animated: true)
                
            }
        })
    }

```
