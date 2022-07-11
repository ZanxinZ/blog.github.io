---
title: "Optional Chaining"
date: 2022-05-24T16:05:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---

A process for querying and calling properties, methods, and subscripts on an optional that might currently be `nil` .Multiple queries can be chained together, and the chain fails gracefully if any link in the chain is `nil` .

## Optional Chaining as an Alternative to Forced Unwrapping

- `!` force unwrapping triggers a runtime error when the optional is `nil` .
- Use the optional chaining to check if the optional value querying is succeed.
    - the chain return optional value is `nil` , the optional chaining fail.
    - the chain return optional contains a value, the optional chaining succeed.

Force Unwrapping

```swift
class Person {
    var bag: Bag?
}
class Bag {
    var countOfPen = 1
}

var p = Person()
print(p.bag!.countOfPen)  // It triggers a runtime-error because the 'bag' is nil, it's not graceful.
// If the bag has a non-nil value, the code above will succeeds.
```

Optional Chaining. Use the question mark in place of the exclamation point

```swift
if let count = p.bag?.countOfPen {
    print("The count of pen is \(count).")
} else {
    print("Unable to retrive the count.")
}
// Print: Unable to retrive the count.
```

The `p.bag?.countOfPen` will return a `Int?` although the `countOfPen` is `Int` , it’s a ‘optional chaining’.

(In this querying chain the bag may be `nil`, the result of this chain may be `nil` so the result of the chain should be optional)

## Optional Chaining for Model Classes

```swift
class Person {
    var bag: Bag?
}
class Bag {
    var pens: [Pen] = []
		subscript(i: Int) -> Pen {
        get {
            pens[i]
        }
        set {
            pens[i] = newValue
        }
    }

    var countOfPen: Int {
        pens.count
    }
    var ink: Ink?
    
}
class Pen {
    var name: String
    init(name: String) {
        self.name = name
    }
}

class Ink {
    var weight: Int?
    var band: String?
    func tellBand() -> String? {
        if let band = band, let weight = weight {
            return "\(band): \(weight)"
        } else if band != nil {
            return band
        } else {
            return nil
        }
    }
}
```

Test the optional chaining.

- The bag is `nil` and the ink is `nil` .
    
    ```swift
    var p = Person()
    
    if let count = p.bag?.countOfPen {
        print("The count of pen is \(count).")
    } else {
        print("Unable to retrive the count.")
    }
    
    if let band = p.bag?.ink?.tellBand() {
        print("Get: \(band)")
    } else {
        print("Unable to retrive the band")
    }
    
    // Print:
    // Unable to retrive the count.
    // Unable to retrive the band
    ```
    
- The bag is non-nil and the ink is non-nil.
    
    ```swift
    var p = Person()
    p.bag = Bag()
    p.bag?.pens.append(Pen(name: "Moni"))
    
    if let count = p.bag?.countOfPen {
        print("The count of pen is \(count).")
        p.bag?.ink = Ink()
        p.bag?.ink?.band = "Hero"
        p.bag?.ink?.weight = 40
    } else {
        print("Unable to retrive the count.")
    }
    
    if let band = p.bag?.ink?.tellBand() {
        print("Get: \(band)")
    } else {
        print("Unable to retrive the band")
    }
    
    // Print:
    // The count of pen is 1.
    // Get: Hero: 40
    ```
    
- The chain interrupted when face `nil`.
    
    ```swift
    func createInk() -> Ink {
        print("Create a ink")
        let ink = Ink()
        ink.band = "Pi"
        return ink
    }
    
    var p = Person()
    p.bag?.ink = createInk()   // The function isn't called, because the bag is nil.
    ```
    
- Access the property by subscript.
    
    ```swift
    var p = Person()
    p.bag = Bag()
    p.bag?.pens.append(Pen(name: "Hi"))
    p.bag?.pens[0] = Pen(name: "Mi")
    print(p.bag?[0].name)             // Optional("Mi") // It access by subscript(i: Int)
    ```
    

## Linking Multiple Levels of Chaining

When drill down the property through the optional chain, the result is only one level of optional, no matter how many levels of chaining are used.

```swift
var band = p.bag?.ink?.tellBand() // If the method is called successfully, the band will just be 'String?' 
```

## Chaining on Method with Optional Return Values

```swift
var length = p.bag?.ink?.tellBand()?.count   // The length is optional after the assignment.
```