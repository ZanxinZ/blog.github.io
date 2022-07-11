---
title: "Automatic Reference Counting"
date: 2022-06-12T16:00:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---


Swift use ARC to track and manage the app’s memory usage. ARC frees up the memory used by class instances when those instances are no longer needed. 

Reference counting **applies only to instance of classes.**

Reference:

- strong: retain the obj. (default)
- weak:  don’t retain the object referred to, track the object referred to.
- unowned: don’t retain the object referred to, don’t tract the object referred to.

## How ARC Work

Allocate a chunk of memory to store information about that instance

- type of the instance
- values of any stored properties associated with that instance.

To make sure that instance don’t disappear while they’re needed, ARC tracks how many properties, constants, and variables are currently referring to each class instance.

To make this possible, whenever assign a class instance to a property, constant, or variable, that property, constant, or variable makes a *strong reference* to the instance. The “strong” reference **keeps a firm hold** on that instance, and doesn’t allow it to be deallocated for as long as that strong reference remains.

## ARC in Action

Here we define a class name Person.  Then we define three variables of type `Person?`, they’re automatically initialized with a value of `nil`, and don’t currently reference a `Person` instance.

```swift
class Person {
    let name: String
    init(name: String) {
        self.name = name
        print("Person build: \(name)")
    }
    deinit {
        print("Person deallocated: \(name)")
    }
}

var p1: Person?
var p2: Person?
var p3: Person?
```

And we can create a new `Person` instance and assign it one of these three variables.

```swift
p1 = Person(name: "Mile") // Now it starts the initializor, and prints the content.
// Print: Person build: Mile
```

We can set other reference to `p1`

```swift
p2 = p1 // The assign of reference don't call the initializer.
p3 = p1

// now there are three strong reference to the single Person instance.
```

If assign other two reference with `nil`, the instance still has one strong reference (`p1`), the instance isn’t deallocated.

```swift
p1 = nil
p2 = nil
// The instance isn't deallocated yet.
```

If the only one last reference is broken, the instance will be deallocated.

```swift
p3 = nil
// Print: Person deallocated: Mile

// Now the instacne has no reference, and it's deallocated (When the instance is forgot by all of others, it will leave forever)
```

## Resolving Strong Reference Cycle Between Class Instance

If two class instance hold a strong reference to each other, it’s called *strong reference cycle*. The solution of the strong reference cycle is to define the reference as `weak` or `unowned`.

### Problem

In the function `test()`, the instance `obj` has a strong reference of type `B`, and the instance `anotherObj` has a strong reference of type `A`. Normally, after the obj and the anotherObj are assigned as `nil`, the instance `A()` and `B()` built should  be recycle when the reference count is zero. But in fact, they are not be recycle (The de-initializer of them haven’t been called). They hold on the reference of each other and can not be deallocated by ARC.

```swift
class A {
    var b: B?
    deinit {
        print("Deinit A")
    }
}
class B {
    var a: A?
    deinit {
        print("Deinit B")
    }
}

var obj: A? = A()
var anotherObj: B? = B()

obj!.b = anotherObj
anotherObj!.a = obj

obj = nil
anotherObj = nil

// Two instance haven't been deallocated yet.
```

### Solution

Change the one of the strong reference to be `weak` reference.

```swift
class A {
    var b: B?
    deinit {
        print("Deinit A")
    }
}
class B {
    weak var a: A?
    deinit {
        print("Deinit B")
    }
}

// So with the code below, the instances of A and B will be deallocated.
var obj: A? = A()
var anotherObj: B? = B()

obj!.b = anotherObj
anotherObj!.a = obj

obj = nil
anotherObj = nil

// Print: 
// Deinit A
// Deinit B
```

### Solution with Unowned Reference

- Weak references must be typed as Optional; they do not retain the object referred to, but they **track the object referred to**, and revert to `nil` if that object goes out of existence.
- Unowned references do not retain the object referred to and **do not track the object referred to**, so it's up to you to prevent that object from going out of existence or you may end up with a dangling pointer and a crash.

```swift
class A {
    var b: B?
    deinit {
        print("Deinit A")
    }
}
class B {
    unowned var a: A?
    deinit {
        print("Deinit B")
    }
}
```

### Unowned References and Implicitly Unwrapped Optional Properties

```swift
class Country {
    let name: String
    var capitalCity: City!
    init(name: String, capitalName: String) {
        self.name = name
        self.capitalCity = City(name: capitalName, country: self)
    }
}

class City {
    let name: String
    unowned let country: Country
    init(name: String, country: Country) {
        self.name = name
        self.country = country
    }
}

// Now we can access the capital name of the country directly.
var country = Country(name: "Canada", capitalName: "Ottawa")
print("\(country.name) capital : \(country.capitalCity.name)") 
// Print: Canada capital : Ottawa
```

## Strong Reference Cycles for Closures

When assign a closure to a property, we are assigning a *reference* to that closure. So if we use both strong references, the class instance and a closure will keep each other alive.

### The more Elegant Solution: Closure Capture List

Here the asHTML is a closure property rather than an instance method, we can replace the default value of it with a custom closure.

```swift
class HTMLElement {
    let name: String
    let text: String?

		// Using the lazy, makes the closure can use the "self"
		// The lazy property will not be accessed until 
    //after initialization has been complete and "self" is konwn.
    lazy var asHTML: () -> String = {
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name)/>"
        }
    }
    
    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

let heading = HTMLElement(name: "h1")
let defaultText = "Some default text"

// Pass a custom closure to set the asHTML property.
heading.asHTML = {
    return "<\(heading.name)>\(heading.text ?? defaultText)</\(heading.name)>"
}

print(heading.asHTML())  // <h1>Some default text</h1>
```

Test the strong reference circle.

```swift
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "Hello world!")

print(paragraph!.asHTML())

paragraph = nil
// Now the instance that paragraph hasn't beem deallocated (The meassage of deinit isn't print), 
// because of the strong reference circle between the instance of HTMLElement and the closure .
```

### Resolving Strong Reference Cycles for Closures

Solution: defining a *capture list* as part of the closure’s definition. Declare each captured reference to be a `weak` or `unowned` reference rather than a strong reference.

A capture list defines the rules to use when capturing one or more reference types within the closure’s body. 

Capture list definition

```swift
lazy var someClosure = {
    [unowned self, weak delegate = self.delegate]
    (index: Int, stringToProcess: String) -> String in
    // closure body goes here
}

// If the closure hasn't specific parameter
lazy var someClosure = {
    [unowned self, weak delegate = self.delegate] in
    // closure body goes here
}
```

Correct the `HTMLElement` by capture list `[unowned self]`

```swift
class HTMLElement {
    let name: String
    let text: String?
    lazy var asHTML: () -> String = {
        [unowned self] in
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name)/>"
        }
    }
    
    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

var paragraph: HTMLElement? = HTMLElement(name: "p", text: "Hello world!")

print(paragraph!.asHTML())

paragraph = nil

// Print: 
// <p>Hello world!</p>
// p is being deinitialized

// Up to here, now the instance of type HTMLElement is deallocated.
```

The relationship between the `HTMLElement` and closure is just like the hen and the egg. If they both hold the strong reference, it’s hard to say which one has the priority to destroy the hen. And with the capture list, the `HTMLElement` (hen) has a strong reference link to the closure, and the closure (egg) just have a `unowned` reference link to the hen.