---
title: "Runloop"
date: 2024-12-30T15:38:33+08:00
ShowToc: true
categories: 
tags: 
---

### 问题: 定时器最好设置为 NSRunLoopCommonModes

在 iOS 中，界面滑动时 RunLoop 会切换到 UITrackingRunLoopMode，而默认情况下 NSTimer 运行在 NSDefaultRunLoopMode，导致滑动时 NSTimer 无法触发或与主线程争夺资源，引发性能下降甚至卡顿。

原因

NSTimer 未被正确配置到适当的 RunLoop 模式中，导致滑动和定时器事件无法同时被处理。

解决方法

将 NSTimer 添加到 NSRunLoopCommonModes，使其在滑动模式下也能正常触发：

```objc
NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:0.1
                                                  target:self
                                                selector:@selector(timerFired)
                                                userInfo:nil
                                                 repeats:YES];
[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];
```

swift 实现
```swift
let timer = Timer.scheduledTimer(withTimeInterval: 0.1, repeats: true) { _ in
    self.timerFired()
}
RunLoop.current.add(timer, forMode: .common)
```

iOS 中的 RunLoop 使用 模式（Modes） 来区分和管理不同的事件集合。模式决定了 RunLoop 能够处理哪些事件，它们之间是互相独立的。在运行时，RunLoop 会根据当前的模式过滤事件。

### 常见的 RunLoop 模式
1.	NSDefaultRunLoopMode

    •	描述: 默认模式，处理大多数普通任务。

    •	用途: 用于处理常规的输入事件，例如用户交互、Timer 事件、网络事件等。

    •	特点: 当用户滚动 UIScrollView 等组件时，RunLoop 会切换到其他模式，此模式的任务可能被暂停。

2.	UITrackingRunLoopMode

	•	描述: 滑动追踪模式，用于处理与用户界面滑动相关的事件。

	•	用途: 专门用来处理滚动视图（如 UIScrollView）滑动时的事件。

	•	特点: 在用户交互（如滑动滚动视图）期间，RunLoop 会切换到此模式，暂停其他模式的事件处理。

3.	NSRunLoopCommonModes

	•	描述: 一种特殊的占位模式，允许事件共享到多个模式中。

	•	用途: 将任务或事件标记为“常用模式”，以便在常见的 RunLoop 模式下都能运行（例如滑动和普通任务）。

	•	特点: 包含了 NSDefaultRunLoopMode 和 UITrackingRunLoopMode 等多个模式。
	•	示例:
    ```objc
    [[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];
    ```

4.	GSEventReceiveRunLoopMode

	•	描述: 用于接收系统事件（如触摸事件）。
    
	•	用途: 系统内部使用的模式，通常开发者不会直接接触。

5.	kCFRunLoopDefaultMode

	•	描述: Core Foundation 中的默认模式，与 NSDefaultRunLoopMode 等价。

	•	用途: 底层使用，用于管理普通事件。

6.	kCFRunLoopCommonModes

	•	描述: Core Foundation 的通用模式，与 NSRunLoopCommonModes 等价。

	•	用途: 底层使用，用于标记共享到多个模式的事件。

7.	自定义模式

	•	描述: 开发者可以创建自定义的 RunLoop 模式，处理特定的任务集合。

	•	用途: 在特殊场景下隔离某些事件，避免它们干扰其他任务。

	•	示例:

    ```objc
    CFRunLoopAddCommonMode(CFRunLoopGetCurrent(), CFSTR("MyCustomMode"));
    ```

模式之间的特性
	•	互斥性: RunLoop 在同一时刻只能运行一种模式下的事件。切换模式时，会停止当前模式的事件处理。
	•	优先级: 滑动等高优先级事件会切换到 UITrackingRunLoopMode，暂停其他模式的事件处理。

模式的使用场景
	
•	NSDefaultRunLoopMode: 适用于绝大多数常规任务。

•	UITrackingRunLoopMode: 适用于处理与用户界面滑动相关的任务，如滚动视图。

•	NSRunLoopCommonModes: 适用于需要同时在滑动和普通模式下运行的任务（如动画或高频更新的任务）。

### runloop 特点：

- runloop 时常不会时间步数固定，有些事件需要做，所以定时器没有非常精准，理论上最小精度为0.1 毫秒。 不过由于受Runloop 的影响，会有50 ~ 100 毫秒的误差，所以，实际精度可以认为是0.1 秒