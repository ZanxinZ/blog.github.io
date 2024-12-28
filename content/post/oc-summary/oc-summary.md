---
title: "Oc Summary"
date: 2024-12-28T14:41:19+08:00
ShowToc: true
categories: 
- iOS
tags: 
- Concept
---


## 类和结构体的区别



## 计算属性

```swift
struct Rectangle {
    var width: Double
    var height: Double
    
    // 这是一个计算属性
    var area: Double {
        get {
            return width * height
        }
        set {
            // 假设保持宽高比例不变
            let ratio = width / height
            height = sqrt(newValue / ratio)
            width = height * ratio
        }
    }
}
```

## 关联对象

## Category 和 Extension 的区别

### Category（分类）：

- 可以在不修改原类源代码的情况下给类添加方法
- 不能添加实例变量（存储属性），但可以使用关联对象
- 可以被添加到任何类中，包括没有源码的类
- 在**运行时**添加方法
- 可以有多个分类

### Extension（扩展）：

- 只能在原类的实现文件（.m文件）中添加
- 可以添加实例变量和属性
- 必须在类的主实现文件中实现所有声明的方法
- 在**编译时**添加特性
- 只能有一个扩展

```objc
@interface MyClass ()
// Extension 1
@property (nonatomic, strong) NSString *property1;
@end

@interface MyClass ()
// Extension 2
@property (nonatomic, strong) NSString *property2;
@end

// 编译后相当于只有一个扩展
@interface MyClass ()
@property (nonatomic, strong) NSString *property1;
@property (nonatomic, strong) NSString *property2;
@end
```


OC 可以动态添加属性或方法，但开销较大、类型安全性差、降低代码可维护性和可读性，swift 中限制了这种方式。

```objc
// 使用关联对象添加属性
objc_setAssociatedObject(self, 
                        @selector(dynamicProperty),
                        value,
                        OBJC_ASSOCIATION_RETAIN_NONATOMIC);

// 动态方法解析
+ (BOOL)resolveInstanceMethod:(SEL)sel {
    if (sel == @selector(dynamicMethod)) {
        class_addMethod([self class], 
                       sel,
                       (IMP)dynamicMethodIMP,
                       "v@:");
        return YES;
    }
    return [super resolveInstanceMethod:sel];
}
```

## 日志场景

在添加日志的场景，swift 不直接使用方法交换、或动态添加方法的方式。

### 1.  使用属性包装器（Property Wrappers）实现

```swift
@propertyWrapper
struct Logged<T> {
    private var value: T
    private let label: String
    
    init(wrappedValue: T, label: String) {
        self.value = wrappedValue
        self.label = label
    }
    
    var wrappedValue: T {
        get {
            print("\(label) was accessed")
            return value
        }
        set {
            print("\(label) is being set to \(newValue)")
            value = newValue
        }
    }
}

class Example {
    @Logged(label: "userName")
    var userName: String = "default"
}
```

优势：编译时生成，静态类型检查，运行时几乎没有额外开销。

### 2.可使用代理模式或装饰器模式来实现方法拦截

```swift
protocol ServiceProtocol {
    func doSomething()
}

// 真实服务类
class RealService: ServiceProtocol {
    func doSomething() {
        print("执行实际操作")
    }
}

// 代理类
class LoggingProxy: ServiceProtocol {
    private let realService: ServiceProtocol
    
    init(_ service: ServiceProtocol) {
        self.realService = service
    }
    
    func doSomething() {
        print("方法执行前的日志")
        realService.doSomething()
        print("方法执行后的日志")
    }
}
```

### 3. Mirror API 实现反射，动态获取实例对象的属性信息。

```swift
struct Person {
    let name: String
    let age: Int
}

let person = Person(name: "张三", age: 30)
let mirror = Mirror(reflecting: person)

// 遍历所有属性
for case let (label?, value) in mirror.children {
    print("\(label): \(value)")
}
// 输出:
// name: 张三
// age: 30
```
Mirror 的主要特点：

- 只读访问：只能读取属性值，不能修改
- 类型安全：提供类型安全的反射机制
- 性能开销：相比直接访问属性，使用反射会有一定的性能开销

通常用于以下场景：

- 调试工具的开发
- 对象序列化
- 日志记录系统
- 测试框架的开发

为什么要使用反射，而不使用直接访问的方式？
反射可以忽略属性的类型，获得的是属性的键值对<名称，内容>，不用为每种对象编写重复代码。
以下特定场景必定需要用反射：
- 处理未知类型：当需要处理在编译时不知道具体类型的对象时，反射可以帮助我们检查和操作这些对象
- 通用序列化：编写能处理任意模型对象的JSON/XML序列化工具时，需要用反射来获取对象的属性。

    ```swift
    // 定义一个简单的模型
    struct Person {
        let name: String
        let age: Int
    }
    // 使用Mirror API进行序列化
    func serialize<T>(_ value: T) -> [String: Any] {
        var dict: [String: Any] = [:]
        let mirror = Mirror(reflecting: value)
        
        for child in mirror.children {
            if let label = child.label {
                dict[label] = child.value
            }
        }
        return dict
    }
    // 使用示例
    let person = Person(name: "张三", age: 25)
    let json = serialize(person)
    // 输出: ["name": "张三", "age": 25]

    // 总之，这种方式，配合泛型，可以处理不同类型的实例、不同类型的属性
    ```

- 框架开发：开发通用框架时，需要处理各种不同的类型，反射提供了统一的处理方式
    ```swift
    protocol Validatable {
        func validate() -> Bool
    }

    @propertyWrapper
    struct Validated<T> {
        private var value: T
        private let validator: (T) -> Bool
        
        init(wrappedValue: T, validator: @escaping (T) -> Bool) {
            self.value = wrappedValue
            self.validator = validator
        }
        
        var wrappedValue: T {
            get { value }
            set { value = newValue }
        }
        
        var projectedValue: Validated<T> { self }
    }

    // 让Validated包装器实现Validatable协议
    extension Validated: Validatable {
        func validate() -> Bool {
            return validator(value)
        }
    }

    class FormValidator {
        static func validateForm(_ form: Any) -> Bool {
            let mirror = Mirror(reflecting: form)
            
            for child in mirror.children {
                // 检查属性是否为Validated类型
                if let validated = child.value as? Validatable {
                    if !validated.validate() {
                        return false
                    }
                }
            }
            return true
        }
    }

    // 使用示例
    struct UserForm {
        @Validated(validator: { !$0.isEmpty })
        var username: String
        
        @Validated(validator: { $0.count >= 6 })
        var password: String
        
        // 验证表单
        func isValid() -> Bool {
            return FormValidator.validateForm(self)
        }
    }
    ```
    这个框架的优势：统一处理不同类型的数据和验证规则
    - FormValidator.validateForm 自动检查所有表单字段的验证状态
    - 提供统一的验证接口
    - 支持可扩展的验证规则


- 动态UI生成：根据数据模型动态生成UI时，可以通过反射来读取模型的结构。



