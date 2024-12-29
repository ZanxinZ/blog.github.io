---
title: "ViewController 生命周期"
date: 2024-12-29T18:37:41+08:00
ShowToc: true
categories: 

tags: 
- iOS

---

- **1. 实例化阶段**：init(coder:) 或 init(nibName:bundle:) 被调用
- **2. 加载视图阶段**：loadView → viewDidLoad
- **3. 视图即将显示**：viewWillAppear → viewWillLayoutSubviews
- **4. 视图完成布局**：viewDidLayoutSubviews → viewDidAppear
- **5. 视图显示期间**：viewWillLayoutSubviews/viewDidLayoutSubviews（根据需要多次调用）
- **6. 视图即将消失**：viewWillDisappear
- **7. 视图已经消失**：viewDidDisappear
- **8. 内存警告**：didReceiveMemoryWarning（可能在任何时候发生）
- **9. 销毁阶段**：deinit

```swift
class LifecycleViewController: UIViewController {
    // 1. 初始化
    override init(nibName: String?, bundle: Bundle?) {
        super.init(nibName: nibName, bundle: bundle)
        print("1. 初始化完成")
    }
    
    required init?(coder: NSCoder) {
        super.init(coder: coder)
    }
    
    // 2. 加载视图
    override func loadView() {
        super.loadView()
        print("2. loadView 被调用")
    }
    
    // 3. 视图加载完成
    override func viewDidLoad() {
        super.viewDidLoad()
        print("3. viewDidLoad 被调用")
    }
    
    // 4-5. 视图显示过程
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        print("4. viewWillAppear 被调用")
    }
    
    override func viewWillLayoutSubviews() {
        super.viewWillLayoutSubviews()
        print("5. viewWillLayoutSubviews 被调用")
    }
    
    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()
        print("6. viewDidLayoutSubviews 被调用")
    }
    
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        print("7. viewDidAppear 被调用")
    }
    
    // 6-7. 视图消失过程
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        print("8. viewWillDisappear 被调用")
    }
    
    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        print("9. viewDidDisappear 被调用")
    }
    
    // 8. 内存警告
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        print("收到内存警告")
    }
    
    // 9. 析构
    deinit {
        print("10. 视图控制器被销毁")
    }
}
```