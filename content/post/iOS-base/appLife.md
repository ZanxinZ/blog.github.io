---
title: "App 生命周期"
date: 2024-12-29T12:20:10+08:00
ShowToc: true
categories: 

tags: 
- iOS
---

![appStatus](../appLife/appStatus.png)

- **未运行（Not Running）**：应用尚未启动或被系统终止
- **非活动（Inactive）**：应用在前台运行但不接收事件，如来电或推送通知时
- **活动（Active）**：应用在前台正常运行并可以接收事件
- **后台（Background）**：应用在后台运行，可执行有限的任务
- **挂起（Suspended）**：应用在后台但不执行代码，可能随时被系统终止



1. App 启动 (App Starts)

	•	入口方法： application:didFinishLaunchingWithOptions
	•	该方法是应用启动时的入口点。
	•	通常用来初始化应用程序，例如加载配置文件、设置窗口、配置依赖项等。
	•	根据是否有传入的 URL 参数，流程会有所不同：
	•	有 URL： 转入 application:openURL:sourceApplication:annotation: 处理 URL。
	•	无 URL： 继续进入活跃状态。

2. 应用进入前台 (Foreground Run Event Loop)

	•	入口方法： applicationDidBecomeActive
	•	应用进入前台并开始响应事件。
	•	此时，用户可以与应用正常交互，例如触摸、滑动等。

3. 中断事件 (Interruptions)

	•	例如接听电话、跳转其他应用。
	•	入口方法： applicationWillResignActive
	•	应用即将进入非活动状态（暂停交互）。
	•	适合在这里保存数据或暂停需要持续运行的任务。

4. 进入后台 (Background Run Loop)

	•	入口方法： applicationDidEnterBackground
	•	应用进入后台，此时需要确保应用资源的正确管理：
	•	保存用户数据。
	•	如果需要继续后台运行，需设置 info.plist 或开启后台任务。
	•	如果应用无法在后台继续运行，则可能被系统暂停或终止。

5. 从后台返回前台

	•	入口方法： applicationWillEnterForeground
	•	应用即将重新进入前台，用户将再次与之交互。
	•	在这里可以恢复状态或刷新界面。

6. 应用终止 (App Terminate)

	•	入口方法： applicationWillTerminate
	•	当应用即将被系统终止时调用。
	•	适用于进行清理工作，例如保存重要数据。
	•	如果应用在后台运行时未被恢复，且资源紧张，系统可能直接杀掉应用。

特别流程：

	•	后台运行与终止判断：
	•	如果应用支持后台运行，且 info.plist 配置了相应的权限，则系统会允许部分任务继续在后台完成。
	•	如果不支持，系统可能直接终止应用（Kill）。

总结：
iOS 应用的生命周期方法提供了多个状态切换点，开发者可以利用这些方法处理启动、后台、前台、终止等情况。根据不同的用户行为（如按 Home 键、切换任务、接电话），应用需要正确处理状态变化，确保用户体验流畅和数据安全。


App 生命周期和 viewController 生命周期初学者可能会混淆，可以参考 [viewController 生命周期](../viewControllerLife)