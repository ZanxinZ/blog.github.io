---
title: "Extension"
date: 2022-05-21T16:00:40+08:00
ShowToc: true
summary: Extension add new functionality to an existing class, structure, enumeration, or protocol type.
tags:
- 编程语言
categories:
- Swift
---
Extension add new functionality to an existing class, structure, enumeration, or protocol type.

Extension in Swift can:

- Add computed instance properties and computed type properties
- Define instance methods and type methods
- Provide new initializers
- Define subscripts
- Define and use new nested types
- Make an existing type conform to a protocol

Or even extend a protocol to provide implementations of its requirements or add additional functionality that conforming types.

## Extension Syntax

```swift
extension SomeType {
		
}

extension SomeType: SomeProtocol, AnotherProtocol {

}
```

- If we define an extension to add new functionality to an existing type, the new functionality will be available on all existing instances of that type, even if they were created before the extension was defined.

## Computed Properties

Extension can add new computed properties, but can’t add stored properties or add property observers to existing properties.

```swift
// Extend the Swift's built-in Double type.
extension Double {
    var km: Double { return self * 1000.0}
    var m: Double { return self}
		var mm: Double {
        set {
            self = newValue / 1000.0
        }
        get {
            return self / 1000.0
        }
    }
}

let tall = 22.2.km

print(tall)       // 22200.0
```

## Initializers

Extensions can add new **convenience** initializers to a class, but can’t add new designated initializers or de-initializers to a class.

```swift
struct Size {
    var width = 0.0, height = 0.0
}

struct Point {
    var x = 0.0, y = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
}

var rectOne = Rect()
var rectTwo = Rect(origin: Point(x: 2.0, y: 3.0), size: Size(width: 5.0, height: 4.0))

extension Rect {
    init(center: Point, size: Size) {
        let x = center.x - (size.width / 2)
        let y = center.y - (size.height / 2)
        self.init(origin: Point(x: x, y: y), size: size)
    }
}

var rectThree = Rect(center: Point(x: 3, y: 3), size: Size(width: 4, height: 4))
print(rectThree)
```

## Method

Extensions can add new instance methods and type methods to existing types.

```swift
extension Int {
    func repetitions(task: () -> Void) {
        for _ in 0..<self {
            task()
        }
    }
}

4.repetitions {
    print("good")
}
```

The method in the extension of the class can modify the instance itself(`self`). However, if the method in  extensions of the structures or enumerations want to modify the `self` , the method must mark as `mutatinfg`.

```swift
// The 'Int' is structure.
extension Int {
    mutating func square() {
        self = self * self
    }
}

var res = 3
res.square()
print(res)   // 9
```

## Subscripts

Extension can add new subscripts to an existing type.

```swift
extension Int {
    subscript(digitIndex: Int) -> Int {
        var decimalBase = 1
        for _ in 0..<digitIndex {
            decimalBase *= 10
        }
        return self / decimalBase % 10
    }
}

var res = 12345[1]
print(res)        // 4
```

## Nested Types

```swift
extension Int {
    enum Kind {
        case negative, zero, positive
    }
    var kind: Kind {
        switch self {
        case 0: 
            return .zero
        case let x where x < 0: 
            return .negative
        case let x where x > 0:
            return .positive
        default: 
            return .negative
        }
    }
}

var x = -5
print(x.kind)  // negative
```

Check kind and responds different situations.

```swift
func check(_ nums: [Int]) -> String {
    var str = ""
    for num in nums {
        switch num.kind {
        case .zero: 
            str += "0"
        case .negative:
            str += "-"
        case .positive:
            str += "+"
        }
    }
    return str
}

print(check([1, 2, 0, -2, 1]))  // ++0-+
```