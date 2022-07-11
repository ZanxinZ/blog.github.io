---
title: "Initialization"
date: 2022-04-30T16:00:40+08:00
ShowToc: true
summary: The class's initializer set the original state for the object.
tags:
- 编程语言
categories:
- Swift
---

Initialization is the process of preparing an instance of a class, structure or enumeration for use.

Properties were set as an initial value.

Initializers are the implements of the process of initialization.

The initializers can be override.

Unlike Object-C, Swift initialization has no return value.

## Initialization for an Instance

Classes and structures must set all of their stored properties to an appropriate initial value when using the initializer. **(When assign a default value to a stored property or set its value within an initializer, the property is set directly without calling any property observers)**

```swift
class Cake {
    // Set the 'size' property with a default value 6 when it's defined. It doesn't tirgger any observers.
    var size = 6 {
        didSet {
            print("Size has been set")
        }
    }
    var price: Double {
        didSet {
            print(price)
        }
    }

		// Initializer
    init () {
        price = 0.0
    }
}

var cake = Cake()  // The initializer assign the price as 0.0, it doesn't trigger any observers.
cake.size = 9      // The size's observer triggered.
cake.price = 22    // The price's observer triggered.

// Print: 
// Size has been set
// 22.0
```

## Customizing Initialization

Define multiple initializer for the class.

```swift
class Cake {
    var size = 6 {
        didSet { print("Size has been set") }
    }
    var price: Double {
        didSet { print(price) }
    }
    init () {
        price = 0.0
    }
    init (price: Double) {
        self.price = price 
    }
    init (size: Int, price: Double) {
        self.size = size   // It doesn't trigger any obserbers.
        self.price = price // It doesn't trigger any obserbers.
    }
}
// Now we create three cakes. They will trigger no observers.
var defaultCake = Cake()
var cake = Cake(price: 15)
var myCake = Cake(size: 7, price: 18)
```

Omit Argument Labels

```swift
class Cake {
    var size = 6 
    var price: Double 
    init () {
        price = 0.0
    }
    init (_ price: Double) {
        self.price = price 
    }
    init (_ size: Int, _ price: Double) {
        self.size = size   
        self.price = price 
    }
}
var defaultCake = Cake()
var cake = Cake(15)
var myCake = Cake(7, 18)

// The compiler calls the specific initializer by checking parmeters' pattern, such as type and count.
```

### Optional Property Types

A stored property has “no value”, it will be automatically assigned a default value of `nil` .

```swift
class Cake {
    var size = 6
    var price: Double?   // Optional, it will be assigned as nil.
    init () {
        
    }
}
```

### Assigning Constant Properties During Initialization

Constant properties 

- can be assigned at any point during initialization. Once it’s assigned, we can’t change it any more.
- can’t be modify by a subclass.

Assign the constant property during initialization.

```swift
class Cake {
    let price: Double = 22
    init () {
        price = 22
        // price = 23 // not allowed
    }
}
```

Assign the constant property by default value.

```swift
class Cake {
    let price: Double = 22
    init () {
        // price = 23 // not allowed
    }
}
```

## Default Initializers

Swift provide a default initializer for any structure or class that provide default values for all of its properties.

```swift
class Cake {
    let price: Double = 0
    var desc: String?    // It get nil in default.
}
var cake = Cake()
```

### Member-wise Initializers for Structure Types

Unlike a default initializer, the member-wise initializer can assign the arguments to the properties automatically, even if the properties have no default value.

An example that the properties without default value.

```swift
struct Cake {
    let price: Double
    var desc: String?
}
var cake = Cake(price: 12)
var bigCake = Cake(price: 50, desc: "Big")
```

Another example that the properties with default.

```swift
struct Size {
    var width = 0.0, height = 0.0
}

var small = Size()
var medium = Size(width: 4)
var big = Size(width: 10, height: 10)
```

## Initializer Delegation for Value Types

A initializer can call another initializer to do some work, so to avoid some duplicate code.

### Class type

- It has inheritance. The classes has additional responsibility for ensuring that all stored properties they inherit are assigned a suitable value during initialization.

### Value Type (structures and enumerations)

- Without inheritance, it can only delegate to another initializer that they provide themselves.
- Use `self.init` to refer to other initializers from the same type in the custom initializer.
- Once define a custom initializer for a value type, we will no longer have access to the default initializer or the member-wise initializer.
- Write the custom initializers in an extension when want to use both default initializer and member-wise initializer.

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
    init() {}
    init(origin: Point, size: Size) {
        self.origin = origin
        self.size = size
    }
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

## Class Inheritance and Initialization

Swift design designated initializers and convenience initializers to ensure all stored properties receive an initial value.

### Designated Initializers

They are the primary initializer for class. Every class must have at least one designated initializer.

Classes tend to have very few designated initializers, always have only one.

It just like the `root of a tree` , which initialization takes place, and through which the process continues up the superclass chain.

Syntax:

```swift
init( parmeters ) {}
```

### Convenience Initializers

They are secondary supporting initializers for a class.

It can call the designated initializer by `self.init()` .

Override the convenience initializers to provide multiple way to create an instance of the class.

Syntax:

```swift
convenience init( parmeters ) {}
```

### Initializer Delegation for Class Types

Rules for delegation calls between initializers:

1. A designated initializer must call a designated initializer from its immediate superclass.
2. A convenience initializer must call another initializer from the same class.
3. A convenience initializer must ultimately call a designated initializer.

### Two-Phase Initialization

The initialization process:

- First phase: each stored property is assigned an initial value.
- Second phase: each class is given the opportunity to customize its stored properties.
- Instance ready to use.

Safety Check (Swift check during compile-time):

1. A designated initializer must ensure that all of the properties introduced by its class are initialized before it delegate up to a **superclass** initializer.
2. A designated initializer must delegate up to a superclass initializer before assigning a value to an inherited property. If it doesn’t, the new value the designate initializer assign will be overwritten by the superclass as part of its own initialization.
3. A convenience initializer must delegate to another initializer before assigning a value to any property. If doesn’t, the new value the convenience initializer assign will be overwritten by it own class’s designated initializer.
4. An initializer can’t call any instance methods, read the values of any instance properties, or  refer to `self` as a value until after the first phase of initialization is complete.

Process the delegate in the subclass:

- First phase of the initialization.
- Ensure all properties were initialized.
- Call superclass’s designated initializer. (Initializer Delegation)
- Assign values to the inherited properties from the superclass.

The call chain: designated init <-  convenience init <- convenience init

```swift
class Cake {
    var size: Int
    var price: Int
    init() {
        size = 6
        price = 10
    }
    convenience init(size: Int) {
        self.init()
        self.size = size
    }
    convenience init(size: Int, price: Int) {
        self.init(size: size)  // Convenience init must call another init before properties assigning. // It will ultimately call designated init.
        self.price = price
    }
}
class CheeseCake: Cake {
    var cheese: Int
		// If subcalss want to customize designated init, it should use 'override' together.
    override init(){
        self.cheese = 10  // Ensure properties' initialization before delegate up.
        super.init()      // Delegate up
        self.cheese = 15  // Assign to property after the delegate up.
        self.size = 7     // Assigning to inherited property  must after delegate up.
        self.price = 22
    }
}
```

The class instance isn’t fully valid until the first phase ends. Properties can only be accessed, and methods can only be called, once the class instance is known to be valid at the end of the first phase.

### Phase 1

Convenience initializer -> ... -> Designated initializer -> superclass’s designated initializer

Initialize own stored properties, and then go to the superclass’s designated initializer to initialize the  inherited properties.

- A designated or convenience initializer is called on a class.
- Allocate memory for new instance, but is not yet initialized.
- Designated initializer confirm all stored properties and initialize them.
- The designated initializer hands off to a superclass initializer to perform the same task for its own stored properties. (run superclass initializer)
- This continues up to the top the chain (the base class’s initializer)
- Once reach the top of the chain, the base class has ensure that all of its stored properties have a value, the instance is now fully initialized, phase 1 complete.

### Phase 2

superclass’s designated initializer -> designated initializer -> ...  -> convenience initializer

Finish the designated initializer and the called convenience initializers.

- Working back down from the top of the chain, each designated initializer in the chain has the option to customize the instance further. Initializers are now able to access `self` and can modify its properties and call its instance’s method.
- Finally, any convenience initializers in the chain have the option to customize the instance and work with `self` .

### Initializer Inheritance and Overriding

In common, subclasses don’t inherit superclass’s initializers by default. But only in certain circumstances when it’s safe and appropriate to do so.

```swift
class CheeseCake: Cake {
    var cheese: Int = 10
}

var cake = CheeseCake(size: 7, price:22)
```

When the designate initializer matches the superclass’s initializer, it need override.

```swift
class CheeseCake: Cake {
    var cheese: Int

    override init(){
        self.cheese = 10
        super.init()     
        self.cheese = 15  
        self.size = 7    
        self.price = 22
    }
}
```

When the designate initializer matches the superclass’s initializer, **omit the** `override` , it’s overwrite. The code below show the overwriting  of convenience initializer from superclass.

```swift
class CheeseCake: Cake {
    var cheese: Int = 10

    convenience init(size: Int, price: Int)  {
        self.init()
        self.size = size
        self.price = price
        self.cheese = 10
    }
}
```

The implicitly call of `super.init()` , it works when the superclass has only one initializer `init()`. 

```swift
class CheeseCake: Cake {
    var cheese: Int
    init(cheese: Int)  {
        self.cheese = cheese
        // super.init() implicitly called here
    }
		convenience init(size: Int, price: Int)  {
        self.init(cheese: 15)
        self.size = size
        self.price = price
    }
}

var cake = CheeseCake(cheese: 15)
```

### Automatic Initializer Inheritance

Rule 1

- If the subclass **doesn’t define any** designated initializers, it automatically inherit **all** of its superclass designated initializer.

Rule 2

- If subclass provides an implementation of **all** of its superclass designated initializers(include the rule 1). Then it automatically inherits **all** of the superclass convenience initializers.

Even if the subclass adds further convenience initializers, these rules work.

The code below is an example of rule 1.

```swift
class CheeseCake: Cake {
    var cheese: Int = 1
}

var cake = CheeseCake()  // It call the init which inherited from the superclass.
```

The code below is an example of rule 2.

```swift
class CheeseCake: Cake {
    var cheese: Int
    override init() {
        self.cheese = 10
        super.init()
    }
    init(cheese: Int) {
        self.cheese = cheese
    }
    convenience init(size: Int, price: Int)  {
        self.init(cheese: 15)
        self.size = size
        self.price = price
    }
}

var cake = CheeseCake(size: 7)  // The initializer 'convenience init(size: Int)' is automactically inherited from the superclass.
```

- A subclass can implement a superclass designated initializer as a subclass convenience initializer as part of satisfying rule 2.
    
    ```swift
    class Cake {
        var size: Int
        var price: Int
        init() {
            size = 6
            price = 10
        }
        init(size: Int, price: Int) {
            self.size = size
            self.price = price
        }
        convenience init(size: Int) {
            self.init()
            self.size = size
        }
    }
    class CheeseCake: Cake {
        var cheese: Int
        override init() {
            self.cheese = 10
            super.init()
        }
        init(cheese: Int) {
            self.cheese = cheese
            super.init()
        }
    		// Implement superclass designated initializer as a subclass convenience
        override convenience init(size: Int, price: Int)  {
            self.init(cheese: 15)
            self.size = size
            self.price = price
        }
    }
    
    var cake = CheeseCake(size: 7) 
    ```
    

### Designated and Convenience Initializers in Action

Classes don’t have a default member-wise initializer. (Structure )

```swift
class Food {
    var name: String
    init(name: String) {
        self.name = name
    }
    convenience init() {
        self.init(name: "default")
    }
}

class Pie: Food {
    var area: Int
    override convenience init(name: String) {
        self.init(name: "default", area: 4)
        self.name = name  // It must call after the super.init
    }
    init(name: String, area: Int) {
        self.area = area
        super.init(name: name)
        self.name = name
    }
}

class ApplePie: Pie {
    
}

var pie = ApplePie()
var applePie = ApplePie(name: "pumpPie")
var bigApplePie = ApplePie(name: "pumpPie", area: 20)

print("\(pie.name): \(pie.area)")
print("\(applePie.name): \(applePie.area)")
print("\(bigApplePie.name): \(bigApplePie.area)")
```

- Result:
    
    default: 4
    pumpPie: 4
    pumpPie: 20
    

## Fail-able Initializers

Place a question mark after the `init` keyword(`init?`).

we can‘t define a fail-able and a non-fail-able initializer with the same parameter types and names.

Fail-able initializer

- create an optional value of the type it initializes.
- write `return nil` within a fail-able initializer to indicate a point at which can trigger initialization failure. (don’t use `return` keyword to indicate the initialization success)

Example:

```swift
// Int(exactly: )
let num: Double = 22
let pi = 3.1415
if let value = Int(exactly: num) {
    print("\(num) \(value)")
} else {
    print("conversion fail")
}
// Print: 22.0 22

// The value not match the type int
let num: Double = 22.1
let pi = 3.1415
if let value = Int(exactly: num) {
    print("\(num) \(value)")
} else {
    print("conversion fail")
}
// Print: conversion fail
```

Use a fail-able initializer init a structure and check if the initialization succeeded.

```swift
struct Animal {
    let species: String
    init?(species: String) {
        if species.isEmpty { return nil}
        self.species = species
    }
}
let creature = Animal(species: "Giraffe")

if let giraffe = creature {
    print("The giraffe init succeeded")
} else {
    print("The giraffe init fail")
}
// Print: The giraffe init succeeded

let cat = Animal(species: "")
if let c = cat {
    print("cat inited successful")
} else {
    print("cat init fail")
}
// Print: cat init fail

// The empty string has a length of 0, but it's not a nil. 
```

### Fail-able Initializer for Enumerations

If the given state is not found in the cases, return `nil` .

```swift
enum Rank {
    case low, midium, high
    init?(rank: String) {
        switch rank {
        case "L":
            self = .low
        case "M":
            self = .midium
        case "H":
            self = .high
        default:
            return nil
        }
    }
}

var rank = Rank(rank: "H")
print(rank)  // Optional(Page_Contents.Rank.high)
var r = Rank(rank: "C")
print(r)     // nil
```

Use in rawValue

```swift
enum Rank: String {
    case low = "L", midium = "M", high = "H"
    
}

var rank = Rank(rawValue: "H")
print(rank)  // Optional(Page_Contents.Rank.high)
var r = Rank(rawValue: "C")
print(r)     // nil
```

### Propagation of Initialization Failure

- A fail-able initializer of a class, structure, or enumeration can delegate across to another fail-able initializer from the same class, structure, or enumeration.
- A subclass fail-able initializer can delegate up to a superclass fail-able initializer.
- A fail-able initializer can delegate to a non-fail-able initializer.

If delegate to another initializer that cause initialization fail, the entire initialization process fails immediately, and no further initialization code is executed.

Here is an example that the subclass fail-able initializer delegate to the superclass fail-able initializer:

```swift
class Product {
    let name: String
    init?(name: String) {
        if name.isEmpty { 
            print("Name empty, super class init fail")
            return nil
        }
        self.name = name
    }
}
class CartItem: Product {
    let quantity: Int
    init?(name: String, quantity: Int) {
        if quantity < 1 { 
            print("No Quantity, subclass init fail")
            return nil
        }
        self.quantity = quantity
        super.init(name: name)
    }
}

var itemNoName = CartItem(name: "", quantity: 2)
var itemNoQuantity = CartItem(name: "Apple", quantity: 0)
var item = CartItem(name:"Pen", quantity: 3)
print(itemNoName)
print(itemNoQuantity)
print(item)

// Print:
// Name empty, super class init fail
// No Quantity, subclass init fail
// nil
// nil
// Optional(Page_Contents.CartItem)
```

### Overriding a Fail-able Initializer

- Override a superclass fail-able initializer in a subclass.
- Override a superclass fail-able initializer **with a subclass non-fail-able initializer**.
- The only way to delegate up to the fail-able initializer is to **force-unwrap the result** of the fail-able initializer.
- Can’t override a non-fail-able initializer with a fail-able initializer.

```swift
class Doc {
    var name: String?
    init() {}
    init?(name: String) {
        if name.isEmpty { return nil}
        self.name = name
    }
		convenience init(size: Int, name: String) {
        self.init(name: name)!  // Delegate up to the fail-able initializer as part of the implementation. 
				// If the name is empty, it will trigger runtime-error.
    }
}

class FileDoc: Doc {
    override init() {
        super.init()
        self.name = "[Untilted]"
    }
		// Override the superclass fail-able initializer with a subclass non-fail-able initializer.
    override init(name: String) {
        super.init()
        if name.isEmpty {
            self.name = "[Untitled]"
        } else {
            self.name = name
        }
    }
}
```

Use forced unwrapping in an initializer to call a fail-able initializer from the superclass as part of the implement of a subclass’s non-fail-able initializer.

```swift
class FileDoc: Doc {
    override init() {
        super.init(name: "[Untitled]")!
    }
}

class MessageDoc: Doc {
    var size: Int
    init(size: Int) {
        self.size = size
        super.init(name: "[Untitled]")!
    }
}
```

### `init!` Fail-able Initializer

The `init!` will implicitly unwrapped the optional result.

```swift
class Doc {
    var name: String?
    init() {}
    init!(name: String) {
        if name.isEmpty { return nil}
        self.name = name
    }
}

class FileDoc: Doc {
    override init() {
        super.init(name: "[Untitled]")
    }
}
```

## Required Initializers

Write the `required` modifier before the definition of a class initializer to indicate that every subclass of the class must implement that initializer.

Don’t write the `override` keyword.

```swift
class Animal {
    required init() {
        print("Fine")
    }
}

class Cat: Animal {
    required init() {
        print("Cat")
    }
}
var cat = Cat()

// Print:
// Cat
// Fine
```

## Setting a Default Property Value with a Closure or Function

The closure init before the initializer, so we can’t use `self` within the closure.

With parentheses, the closure’s return value (or result) will be assign to the  property.

```swift
class Cake {
    var name: String = { 
        print("fine")
        return "cake"
    }()
		// The parentheses after the closure means that it's a calling. The closure executed immediately.
}

var cake = Cake()  // Print: fine
print(cake.name)   // Print: cake
```

Without parentheses, the closure itself is to assign to the property.

```swift
class Cake {
    var name: () -> String = {  
        print("fine")
        return "cake"
    }
}

var cake = Cake()  // Hasn't called the closure
print(cake.name)   // Print: (Function)
cake.name()
```

Example: 

```swift
class ChessBoard {
    var boardColors: [Bool] = {
        var isBlack = false
        var board: [Bool] = []
        for i in 1...8 {
            for j in 1...8 {
                board.append(isBlack)
                isBlack = !isBlack
            }
        }
        return board
    }()
    
    func getIsBlack(row: Int, col: Int) -> Bool {
        return boardColors[(row * 8) + col]
    }
    
}

var b = ChessBoard()
print(b.getIsBlack(row: 2, col: 3)) // Print: true
print(b.getIsBlack(row: 4, col: 4)) // Print: false
```