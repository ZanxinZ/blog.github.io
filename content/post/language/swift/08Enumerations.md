---
title: "Enumerations"
date: 2022-04-10T16:08:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---


## Syntax

```swift
enum Direction {
		case up
		case down
		case left
		case right
}
```

Multiple case can appear on a single line, separated by commas:

```swift
enum {
		case up, down, left, right
}
```

Use the Enumeration

```swift
var dir = Direction.up
```

When we want to **modify** the var after the initialized, we can use a shorter form of the enumeration.

```swift
var dir = Direction.up
dir = .down    // The value's type has been inferred when the value is in initializing.
```

## Matching Enumeration Values with a Switch Statement

```swift
enum Direction {
    case up
    case down
    case left
    case right
}
var dir = Direction.right
switch dir {
case .up:
    print("Go up")
case .down:
    print("Go down")
case .left:
    print("Go left")
case .right:
    print("Go right")
}
```

## Iterating over Enumeration Cases

Conform to the `CaseIterable` protocol,  to make the enumeration’s case be iterable.

```swift
enum Beverage: CaseIterable {
    case coffee, tea, juice
}
let cases = Beverage.allCases
for c in cases {
    print(c)
}
```

- Result:
    
    coffee
    tea
    juice
    

## Associated Values

Here has two reference with associated values of type `(Int, Int, Int, Int)` or `(String)`, use enumeration can choose one of them.

```swift
enum Barcode {
		case upc(Int, Int, Int, Int)
		case qrCode(String)
}
```

Use the enumeration to create value, choose a type of barcode

**But They can store only one of them at any given time.**

```swift
var product = Barcode.upc(8, 810, 222, 888)
product = .qrCode("KINGKIYK")              // Assign another type to the same product.
```

Use `switch` to check the type, and use `let` or `var` to extract each associated value.

```swift
switch product {
case .upc(let numberSystem, let manufacturer, let product, let check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case .qrCode(let productCode):
    print("QR code: \(productCode).")
}
```

If all the associated values are extracted as constants or if all are extracted as variable, we can use `var` or `let` annotation before the case name, for brevity. 

```swift
switch product {
case let .upc(numberSystem, manufacturer, product, check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
```

## Raw Values

As an alternative to associated values, enumeration cases come pre-populated with default values(raw values), which are all of the same type.

Each raw value must be unique within its enumeration declaration.

```swift
enum somechar: Character {
		case tab = "\t"
		case lineFeed = "\n"
		case carriageReturn = "\r"
}
```

In Enumeration:

- Raw values are set by default, choose one to use when we need.
- Associated values are set before we want to use it.

## Implicitly Assigned Raw Values

When we define an enumeration that store integer or string raw values, it’s no need to explicitly assign for each case, Swift can infer from the first one case.

- Implicitly assign with integer

```swift
enum Day: Int{
    case Sunday = 1, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday
}
print(Day.Monday.rawValue)
// Print: 「2」
```

- Implicitly assign with string
    
    ```swift
    enum Direction: String{
        case up, down, left, right
    }
    
    print(Direction.left.rawValue)
    // Print: 「left」
    ```
    

## Initializing from a Raw Value

```swift
enum Day: Int{
    case Sunday = 1, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday
}

enum Direction: String{
    case up, down, left, right
}

let day = Day(rawValue: 3)     // 'day' is of type 'Day?' and equals to Day.Tuesday 
let dir = Direction(rawValue: "right")
let other = Day(rawValue: 8)   // Not found rawValue, 'other' will be 'nil'

print(day!)
print(dir)
print(other)
```

- Result:
    
    Tuesday
    Optional(Page_Contents.Direction.right)
    nil
    

## Recursive Enumerations

Base on the associated values, recursively use the the associated operation.

Use the `indirect` keyword to indicate that the associated value will be called recursively.

The `<T>` indicate to use generic type.

```swift
enum Arithmetic<T> {
    case num(T)
    indirect case add(Arithmetic, Arithmetic)
    indirect case multiply(Arithmetic, Arithmetic)
}
```

Another way to indicate the `indirect` :

```swift
indirect enum Arithmetic<T> {
    case num(T)
    case add(Arithmetic, Arithmetic)
    case multiply(Arithmetic, Arithmetic)
}
```

Use the recursive, and access the value with recursive function.

```swift
let five = Arithmetic<Int>.num(5)
let nine = Arithmetic<Int>.num(9)

let sum = Arithmetic.add(five, nine)
let multiply = Arithmetic.multiply(sum, five)

func evaluate(_ expression: Arithmetic<Int>) -> Int {
    switch expression {
    case let .num(value):        // Here use the value binding to match the expression.
        return value
    case let .add(first, second):
        return evaluate(first) + evaluate(second)
    case let .multiply(first, second):
        return evaluate(first) * evaluate(second)
    }
}
print(evaluate(sum))        // 5 + 9 == 14
print(evaluate(multiply))   // (5 + 9) * 5 == 70
```

- Result:
    
    14
    70