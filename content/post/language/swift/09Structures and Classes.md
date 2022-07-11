---
title: "Structures and Classes"
date: 2022-04-10T16:12:40+08:00
summary: The object oriented programming.
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---


Structures and classes are the basic building blocks of the program’s code.

They have properties and functions.

We can define a structure or class in a single file, the external interface of that structure or class  is automatically made for other code to use.

## The common things:

- Define properties to store values
- Define methods to provide functionality
- Define subscripts to provide access to their values using subscript syntax
- Define initializers to set up their initial state
- Be extended to expand their functionality beyond a default implementation
- Conform to protocols to provide standard functionality of a certain kind

## The additional capabilities that classes support:

- Inheritance enables one class to inherit the characteristics of another.
- Type casting enables you to check and interpret the type of a class instance at runtime.
- Deinitializers enable an instance of a class to free up any resources it has assigned.
- Reference counting allows more than one reference to a class instance.

## Usages

### Definition

```swift
struct Cat {
		var weight = 3
		var legCount = 4
}

class Dog {
		var weight = 5
		var legCount = 4
		var cloth = Cloth()  // Every dog has a cloth in red color.
		var name = String?
}

class Cloth {
		var color = "red"
		var width = 15
		var length = 45
}
```

### Create Instance

```swift
let cat = Cat()

let dogA = Dog()
let dogB = Dog()

let cloth = Cloth()
```

### Access Properties

```swift
print(cat.legCount)

print(dogA.legCount)

print(dogB.cloth.color)
```

### Initialize

```swift
class Dog {
    var weight: Int
    var legCount: Int
    init (){
        self.weight = 5
        self.legCount = 4
    }
    init (weight: Int, legCount: Int = 4){
        self.weight = weight
        self.legCount = legCount
    }
}

let dog = Dog(weight: 8)

print(dog.weight)   // 8
print(dog.legCount) // 4
```

## Structures and Enumerations Are Value Types

Value type will be **copied the whole value** when it’s being assigned.

- integers, floating-point numbers, Booleans, strings, **arrays** and **dictionaries** are value types. They are implemented as structures behind the scenes.
    
    Collections like String, array, dictionary use an optimization to reduce the performance cost of copying. They aren’t been copied immediately when be assigned. It creates a reference and share the memory of the collection, **collection just be copied when the modification occurs.**
    

### Value Copy

```swift
let count = 4
let legCount = count  // value copy

struct Rect {
		var width = 20
		var height = 10
}

var window = Rect()
   
// It's value copy, even the 'window' and the 'table' has the same width and height, they are two different instance of Rect. 
var table = window

table.width = 25
print(table.width)   // Print: 「25」
print(window.width)  // Print: 「20」. It hasn't affect the width of the window.

enum Color: Int{
    case green = 1, red, blue, white
    mutating func toWhite() {
        self = .white
    }
}

var windowColor = Color.blue
let tableColor = windowColor    // value copy

windowColor.toWhite()
print(windowColor)  // white
print(tableColor)   // blue
```

## Classes Are Reference Types

Swift just **copy the reference** of the instance when  assigned an instance to another one.

### Reference Copy

```swift
class Dog {
    var age = 2
}

let labulado = Dog()
let pet = labulado  // Just copy the reference.

pet.age += 1  // The reference has't change, just change the property 'age' of the instance.

print(labulado.age)  // 3
```

The `labulado` and `pet` are different reference, but they share one instance of the `Dog` . 

## Identity Operators

- identical to (===)
- not identical to (!==)

Use identity operators to check whether two constants or variables refer to the same single instance.

```swift
if labulado === pet {
		print("Refer to the same instance")
} else {
		print("Refer to different instance")
}
```

## Equal Operators

- equal to (==)
- not equal to (!=)

The **basic value type** can use the equal operators directly.

```swift
var s = "dd"
var a = "dd"
print(s == a)
```

We must define the equal function for our own customs classed and structures, before we use the equal operators.

```swift
class Dog {
    var age = 2
		// The equal function(operator override)
    static func == (left: Dog, right: Dog) -> Bool {
        return left.age == right.age
    }
}

let pet = Dog()
pet.age += 1

let myPet = Dog()
myPet.age = 3

print(pet === myPet)  // false, they are refer to different instance of Dog

print(pet == myPet)   // true, here use the equal function above to check whether two instances are equal. 
```

## Pointer

The reference in Swift is similar to a pointer in C, but  isn’t a direct pointer to an address in memory.

The standard library provides pointer and buffer types that we can use if we want to interact with pointer directly.