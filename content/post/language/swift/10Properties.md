---
title: "Properties"
date: 2022-04-17T16:12:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---


- Stored properties
    
    Only provided by **classed and structures**.
    
- Computed properties
    
    **Provide by classes, structures, enumerations.**
    

Usually associated with instance of a particular type, can be associated with the type itself(type properties).

Can define property observers to monitor changes in a property’s value, which can respond to with custom action.

Can use a property wrapper to reuse code in the getter and setter.

## Stored Properties

```swift
**struct Setting {
    var width: Int
    let height: Int  
}
var appSetting = Setting(width: 100, height: 200)
appSetting.width = 150

let constSetting = Setting(width: 110, height: 210)
//constSetting.width = 120  // This will trigger a compile-time error, the struct is of const value type.**

```

## Lazy Stored Properties

Its initial value isn’t calculated until the first time it’s used.

Lazy stored properties must be a variable, it will be assigned at the first time it’s used. 

Constants can’t be lazy, because it always have a value before initialization completes.

```swift
class DataImporter {
    var fileName = "data.txt"
    func readFromFile(path: String) -> String {
        return "some " + path
    }
}
class DataManager {
    lazy var importer = DataImporter()
    var data: [String] = []
}
let manager = DataManager()
manager.data.append("Happy")
manager.data.append("Time")
// Here the manager.importer has not yet been created.

var str = manager.importer.readFromFile(path: manager.data[0]) // Here the manager.importer is created, and then call the function of the manager.importer

print(str)
// Print: 「some Happy」
```

- If a property marked with the `lazy` modifier is accessed by multiple threads simultaneously and the property hasn’t yet been initialized, there’s no guarantee that the property will be initialized only once.

### Stored Properties and Instance Variable

In Objective-C or C++, the definition and initialization of an instance variable are separated in different places. 

Swift cancels the instance variable rules, and use **a simple way to declare the propertied** of an instance, define the name, type, memory management  characteristics in a single location.

## Computed Properties

In classes, structures, and enumerations, we provide a **getter and setter** to retrieve and set properties and values indirectly.

Using computed properties, we can compute something else of the class’s from the other properties.

```swift
struct Bag {
    var bookCount: Int = 0
    var penCount: Int = 0
    var objectCount: Int {
        get {
            return bookCount + penCount
        }
        set (count) {
            bookCount = Int(Double(count) * 0.8)
            penCount = Int(Double(count) * 0.2)
        }
    }
}

var bag = Bag(bookCount: 1, penCount: 2)

print(bag.objectCount)  // Here use the getter. Print 「3」

bag.objectCount = 10    // Here use the setter. The temporary parameter 'count' is assignes to 10.

print(bag.objectCount)  // 10
print(bag.bookCount)    // 8
print(bag.penCount)     // 2
```

The setter has a shorter form of declaration. It must use the `newValue` to grab the temporary parameter.

```swift
set {
        bookCount = Int(Double(newValue) * 0.8)
        penCount = Int(Double(newValue) * 0.2)
    }
```

The getter has a shorter form of declaration. It write the code with one line and as the return expression.

```swift
get {
		 bookCount + penCount
}
```

### Read-Only Computed Properties

```swift
struct Bag {
    var bookCount: Int = 0
    var penCount: Int = 0
    var objectCount: Int {
        get {
            bookCount + penCount
        }
    }
}
```

Read-Only in a shorter form:

```swift
struct Bag {
    var bookCount: Int = 0
    var penCount: Int = 0
    var objectCount: Int {
       return bookCount + penCount
    }
}
```

Omit the `return` 

```swift
struct Bag {
    var bookCount: Int = 0
    var penCount: Int = 0
    var objectCount: Int {
       bookCount + penCount
    }
}
```

The read-only computed properties are useful for an external user to discover  the `Bag` ’s current status.

## Property Observers

The observer can observe and respond to changes in a property’s value.

The observer can’t be provide together with getter or setter.

Only can add property in these places:

- Stored properties that we define.
- Stored properties that we inherit.
    
    For an inherit property, add a property observer by overriding that property in a subclass.
    
- Computed properties that we inherit.
    
    Use property’s setter to observe and responds to value changes, instead of creating an observer.
    

Define either or both of these observers on a property.

- `willSet` is called before the value is stored. (pass the new property value as a constant, it has a default name of `newValue`)
- `disSet` is called immediately after the new value is stored.(pass the last old property value as a constant, it has a default name of `oldValue`)

```swift
struct Bag {
    var bookCount: Int = 0
    var penCount: Int = 0
    var objectCount: Int {
        willSet (newCount) {
            print("Will set the count \(newCount)")   // 2
        }
        didSet {
            print("Has set the count, the olValue is \(oldValue), and add \(objectCount - oldValue)")  // 0 2
        }
    }
}
var bag = Bag(objectCount: 0)

bag.objectCount = 2  // Here trigger the wiilSet and then trigger the didSet. The newValue is 2, and the oldValue is 0.
print("After change, objectCount: \(bag.objectCount)") // 2
```

The observer in the superclass can observe the properties’ action in the subclass.

- The subclass set the superclass’s property after the superclass’s initializer has been  called.
- If the superclass’s initializer hasn’t been called, the observer can not work.

## Property Wrappers

It can wrap the data and the code together as a code block. Using property wrapper can reduce the repeated codes.

Here is an example that store a number less than 12 or equals to 12. The wrappedValue must used explicitly.

```swift
@propertyWrapper
struct Num {
    private var number = 0
    var wrappedValue: Int {
        get {return number}
        set {number = min(newValue, 12)}
    }
}

```

Using

```swift
struct Rectangle {
    @Num var width: Int   // It declares that the var use the rule of the propertyWrapper
    @Num var height: Int
}

var rect = Rectangle()

print(rect.width)  // Call the 'get' function

rect.height = 10   // Call the 'set' function
```

Another way to use the propertyWrapper

```swift
struct Rectangle {
    private var _height = Num()
    private var _width  = Num()
    var height: Int {
        get{ return _height.wrappedValue }
        set{ _height.wrappedValue = newValue}
    }
    var width: Int {
        get{ return _width.wrappedValue}
        set{ _width.wrappedValue = newValue}
    }
}
```

### Setting Initial Values for Wrapped Properties

In default, use the `wrppedValue` to catch the input implicitly.

Override the initial function so to provide several ways to to init a `Num` .

```swift
@propertyWrapper
struct Num {
    private var maximum: Int
    private var number: Int
    var wrappedValue: Int {
        get { return number}
        set { number = min(newValue, maximum)}
    }
    init() {
        maximum = 12
        number = 0
    }
    init(wrappedValue: Int) {
        maximum = 12
        number = wrappedValue
    }
    init(wrappedValue: Int, maximum: Int) {
        self.maximum = maximum
        number = wrappedValue
    }
}
```

The initialization without parameter.

```swift
struct ZeroRectangle {
    @Num var width: Int
    @Num var height: Int
}
var zeroRect = ZeroRectangle()
```

The initialization with one default parameter `wrappedValue` .

```swift
struct UnitRectangle {
    @Num var width: Int = 1  // It call the init
    @Num var height: Int = 1
}

var unitRect = UnitRectangle()

// Or in this way

struct UnitRectangle {
    @Num(wrappedValue: 1) var width: Int 
    @Num(wrappedValue: 1) var height: Int
}

var unitRect = UnitRectangle()
```

The initialization with two parameters.

```swift
struct NarrowRectangle {
    @Num(wrappedValue: 2, maximum: 5) var width: Int
    @Num(wrappedValue: 3, maximum: 4) var height: Int
}

var narrowRect = NarrowRectangle()

// Or in this way

struct NarrowRectangle {
    @Num(wrappedValue: 2, maximum: 5) var width: Int
    @Num(maximum: 4) var height: Int = 3
}

var narrowRect = NarrowRectangle()
```

The default parameter `wrappedValue` is used to pass input implicitly.

### Project a Value From a Property Wrapper

Declare the var name `projectedValue` in a property wrapper.

It can store some information such as  **track the change of the property**, and then the outside caller can get the information from the projectedValue.
It can be accessed via the dollar sign `$` , when the outside caller has the permission to access.

```swift
struct Num {
    private var  number: Int
    private(set) var projectedValue: Bool  // The projectedValue's set function is declare as private.
    var wrappedValue: Int {
        get { return number}
        set { 
            if newValue > 12 {
                number = 12
                projectedValue = true
            } else {
                number = newValue
                projectedValue = false
            }
        }
    }
    init() {
        self.projectedValue = false
        self.number = 0
    }
}

struct Rectangle {
    @Num var width: Int
}

var rect = Rectangle()
rect.width = 15 // The number is private, it will be affect via the `set` function.

print(rect.$width)  // Use the dollar sign to access the projectedValue.

// rect.$width = false // It can't work, the outside caller can't call the projectedValue's private function 'set'
```

## Global and Local Variables

- Global
    
    It is defined outside any function, method, closure, or type context.
    
    Global constants and variables are **always** **lazily**.
    
- Local
    
    It is defined in  any function, method, closure, or type context.
    
    Local constants and variables are never computed lazily.
    

### Stored Variables

- provide storage for a value of a certain type and allow that value to be set and retrieved.

Observer is available for the stored variable and computed variable in a global or local scope.

Property wrapper can be use to  a stored variable, but not to a global variable or a computed variable.

Here’s  a function’s local stored variable (count) use the wrapper property (Num).

```swift
func doSomething() {
    @Num var count: Int
    _count.number = 12   // The 'number' requires the wrapper 'Num' so it use a underscore to indicate the wrapper.
    print(_count.number)
}
```

## Instance Properties

Every time we create a new instance of that type, it has it own set of property values, separate from any other instance.

## Type Properties

- If we define a property belong to the type itself, it’s called type property. All instance of that type share this one type property. (like static property in Java class)
- Stored type properties can be variables or constants.
- Computed type properties are always declared as variable properties, in the same way as computed instance properties.
- Need default value.
- Lazily initialized on first access. Be initialized only once, even when accessed by multiple threads simultaneously.

### Type Property Syntax

Define type property with `static` keyword.

In a class, use `class` instead the `static` to override the superclass’s implementation.

```swift
struct ShortSize {
    static var name = "Short"
    private static var store: Int = 1 
    static var size: Int {
        get { return store}
        set { store = newValue}
    }
}

enum MediumSize {
    static var name = "Medium"
    private static var store: Int = 4
    static var size: Int {
        get { return store }
        set { store = newValue}
    }
}

class LongSize {
    static var name = "Long"
    private static var store: Int = 4
    static var size: Int {
        get { return  store}
        set { store = newValue}
    }
    
    class var sizeFromSuper: Int {
        get { return store }
        set { store = 2 * newValue}
    }
}

// Use the type properties
print(ShortSize.name)
print(ShortSize.size)
```

Setting the Type Properties

- setting
    
    ```swift
    struct ShortSize {
        static var name = "Short"
        private static var store: Int = 1 
        var size: Int {
            get { return ShortSize.store}
            set { ShortSize.store = newValue}
        }
    }
    
    var shortSize = ShortSize()
    var shortSizeAnother = ShortSize()
    
    print(shortSize.size)       
    print(shortSizeAnother.size)
    
    shortSize.size = 15         // It will change the sharing variable 'store' (type property)
    print(shortSize.size)
    print(shortSizeAnother.size)
    ```
    
    - Result
        
        1
        1
        15
        15
        

Use the observer to respond to the change of the variable and to affect the type property.

```swift
struct Channel {
    static let maxLevel: Int = 10
    static var maxInput: Int = 0
    var currentLevel: Int {
        didSet {
            if currentLevel > Channel.maxLevel {
                currentLevel = Channel.maxLevel  // It set the currentLevel, but it doesn't call the observer again.
            } else if currentLevel > Channel.maxInput {
                Channel.maxInput = currentLevel
            }
            
        }
    }
}

var channel = Channel(currentLevel: 0)
channel.currentLevel += 4
print(Channel.maxInput)
```