---
title: "Advanced Operators"
date: 2022-06-19T16:00:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---


Unlike arithmetic operators in C, arithmetic operators in Swift don’t overflow by default. If want to overflow by default, use the overflow operation begin with ampersand (`&`). For example, the overflow addition operator (`&+`).

It’s so free to define custom infix, prefix, postfix and assignment operators, precedence and associativity values.

## Bitwise Operators

Here we use a function to pad 0 for the number’s print result.

```swift
func pad(num: UInt8, count: Int) -> String {
    var str = String(num, radix: 2)
    var res: String = str
    for _ in 0..<(count - str.count) {
        res = "0" + res
    }
    
    return  res
    
}
```

- `~` NOT
    
    ~1 is 0
    
     ~0 is 1
    
- `&` AND
    
    1 & 1 is 1
    
    1 & 0 is 0
    
    0 * 0 is 0
    
- `|` OR
    
    1 | 1 is 1
    
    1 | 0 is 1
    
    0 | 0 is 0
    
- `^` XOR
    
    1 ^ 1 is 0
    
    1 ^ 0 is 1
    
    0 ^ 0 is 0
    

### Bitwise NOT Operator

Use `~` to do NOT operation.

```swift
var a: UInt8 = 0b11111100
var b = ~a
print(pad(num: b, count: 8))  // 00000011
```

`UInt8` has 8 bits, and can store 0 to 255.

### Bitwise AND Operator

Use `&` to do AND operation.

```swift
let a: UInt8 = 0b11111100
let b: UInt8 = 0b00011010
let c = a & b
print(pad(num: c, count: 8)) // 00011000
```

### Bitwise OR Operator

Use `|` to do OR operator.

```swift
let a: UInt8 = 0b11111100
let b: UInt8 = 0b00011010
let c = a | b
print(pad(num: c, count: 8))  // 11111110
```

### Bitwise XOR Operator

Return a new number whose bits are set to 1 where the input bits are different and are set to 0 where the input bits are the same.

```swift
let a: UInt8 = 0b11111100
let b: UInt8 = 0b00011010
let c = a ^ b
print(pad(num: c, count: 8))  // 11100110
```

### Bitwise Left and Right Shift Operators

Left shift (`<<`) move all bits in a number to the left.

```swift
let a: UInt8 = 0b00111100
let b = a << 1
let c = a << 2
print(pad(num: b, count: 8))  // 01111000
print(pad(num: c, count: 8))  // 11110000
```

### Shifting of the Unsigned Integer

It will place an zero at the left when right shift, and place an zero at the right when left shift.

Right shift (`>>`) move all bits in a number to the right.

```swift
let a: UInt8 = 0b00111100
let b = a >> 1
let c = a >> 2
print(pad(num: b, count: 8))  // 00011110
print(pad(num: c, count: 8))  // 00001111
```

Use bit shifting to encode and decode values within other data types:

```swift
let pink: UInt32 = 0xCC6699
let redComponent = (pink & 0xFF0000) >> 16
let greenComponent = (pink & 0x00FF00) >> 8
let blueComponent = pink & 0x0000FF
print(String(redComponent, radix: 16))   // cc
print(String(greenComponent, radix: 16)) // 66
print(String(blueComponent, radix: 16))  // 99

// By AND operation and shifting, we get the three part of the "pink" color: R G B
```

The CSS color value `#CC6699` is written as `0xCC6699` in Swift’s hexadecimal number representation.

### Shifting Behavior for Signed Integer

I**n the 8-bit signed integer example:**

Signed Integer use its first bit to indicate whether the integer is positive or negative. (0 is positive, 1 is negative)

`00000100` is 4

`11111100` is -4, the first bit 1 indicate that the integer is negative, and the **remain 7 bits** is 124 means that 128 - 124 = -4

So the signed 8-bits can represent **-128(10000000) to 127(01111111)**.

Two’s Complement Representation

The way it’s encoded above is known as a *two’s complement* representation. In this way it has several advantage. 

- Add -4 and -1, it add in binary rule and discard anything that overflow.
    
    (-4) + (-1) = 11
    
    11111100 + 11111111 = 11111011 
    
- arithmetic shift
    - left shift:  an arithmetic shift = a logical shift. It will doubling the number.
    - right shift:  when shift signed integers to the right, apply same rules as for unsigned integers, but fill any empty bits on the left **with the sign bit,** rather than with a zero.
        
        ```swift
        11111110 >> 1 will be 11111111
        01111110 >> 1 will be 00111111
        ```
        
        In this way it ensures that the number will has the same sign after shifting. The shift will moves both positive and negative numbers closer to zero.
        

## Overflow Operators

When the constant or variable can’t hold the value that too large or too small, Swift will raise an error.

```swift
// The num get the max value of Int8
var num = Int8.max

num += 1  // Here will a runtime-error.
```

If we specifically want an overflow condition to truncate the number of available bits, we can use the overflow operators that begin with an ampersand (&).

- Overflow addition (&+)
- Overflow subtraction (&-)
- Overflow multiplication (&*)

### Value Overflow

If we allow the number to overflow, it may overflows in both positive and negative direction.

```swift
var num: UInt8 = UInt8.max      // It's 11111111 (255)
num = num &+ 2

print(pad(num: num, count: 8))  // 00000001

var n: UInt8 = 1
n = n &- 2
print(pad(num: n, count: 8))    // 11111111
```

The overflow operation can be apply to signed integer.

```swift
var signedNum: Int8 = Int8.min     // -128
signedNum = signedNum &- 1
print(String(signedNum, radix: 2)) // 127
```

## Precedence and Associativity

From high to low:

- %
- * /
- + -

```swift
let a = 2 + 3 % 4 * 5
print(a)  // 17
```

**Left associative**: calculate from left to right.

- *
- /
- %
- &
- &*
- +
- -
- |
- ^
- &+
- &-
- is
- as
- as?
- as!
- &&
- .&
- ||
- .|
- .^

**Right associative**: calculate from right to left.

- ??
- :?
- =
- *=
- /=
- %=
- +=
- -=
- <<=
- >>=
- &=
- |=
- ^=
- &*=
- &+=
- &-=
- &>>=
- &<<=
- .&=
- .|=
- .^=

**Isn’t associative**:

- <<
- >>
- &<<
- &>>
- <
- <=
- >
- >=
- ==
- !=
- ===
- !==
- ~=
- .<
- .<=
- .>
- .>=
- .==
- .!=
- ..<
- …

## Operator Methods

Classes and structures can overloading the existing operators.

```swift
struct Vector2D {
    var x = 0.0, y = 0.0
}

extension Vector2D {
    static func + (left: Vector2D, right: Vector2D) -> Vector2D{
        return Vector2D(x: left.x + right.x, y: left.y + right.y)
    }
}

var a = Vector2D(x: 1.0, y: 2.0)
var b = Vector2D(x: 3.0, y: 3.0)
var c = a + b
print(c)  // Vector2D(x: 4.0, y: 5.0)
```

### Prefix and Postfix Operators

```swift
extension Vector2D {
    static prefix func - (vector: Vector2D) -> Vector2D {
        return Vector2D(x: -vector.x, y: -vector.y)
    }  
}
var x = Vector2D(x: 1.0, y: 2.0)
var xx = -x
print(xx)   // Vector2D(x: -1.0, y: -2.0)
```

### Compound Assignment Operators

+=

```swift
extension Vector2D {
    static func += (left: inout Vector2D, right: Vector2D){
        left = left + right
    }
}
var v = Vector2D(x: 1.0, y: 2.0)
var w = Vector2D(x: 4.0, y: -3.0)

v += w
print(v)  // Vector2D(x: 5.0, y: -1.0)
```

### Equivalence Operators

==

One way to make `==`  be valid for enumeration or structure: Conform to the `Equalble` , it has provided method to do that.

```swift
extension Vector2D: Equatable {
    
}

var one = Vector2D(x: 1.0, y: 2.0)
var two = Vector2D(x: 1.0, y: 2.0)

print(one == two)  // true
```

Another way is to implement the method in the extension.

```swift
extension Vector2D: Equatable {
    static func == (left: Vector2D, right: Vector2D) -> Bool {
        return (left.x == right.x) && (left.y == right.y)
    }
}

var one = Vector2D(x: 1.0, y: 2.0)
var two = Vector2D(x: 1.0, y: 2.0)

print(one == two)  // true
```

## Custom Operators

Prefix operator

```swift
prefix operator +++  
extension Vector2D {
    static prefix func +++ (vector: inout Vector2D) -> Vector2D {
        vector += vector
        return vector
    }
}

var some = Vector2D(x: 1.0, y: 2.0)
    +++some

print(some)   // Vector2D(x: 2.0, y: 4.0)
```

### Precedence for Custom Infix Operators

Here we define a new custom infix operator call +-, which belongs to the precedence group `AdditionPrecedence`. 

```swift
infix operator +-: AdditionPrecedence
exthe tension Vector2D {
    static func +- (left: Vector2D, right: Vector2D) -> Vector2D{
        return Vector2D(x: left.x + right.x, y: left.y - right.y)
    }
}

var a = Vector2D(x: 1.0, y: 2.0)
var b = Vector2D(x: 2.0, y: 3.0)
var c = a +- b

print(c) // Vector2D(x: 3.0, y: -1.0)
```

Don’t need to specify a precedence when defining a prefix or postfix operator. When apply both prefix and postfix to the same operand, the postfix operator is applied first.

## Result Builders

If the code without result builder:

```swift
protocol Drawable {
    func draw() -> String
}
struct Line: Drawable {
    var elements: [Drawable]
    func draw() -> String{
        return  elements.map { $0.draw() }.joined(separator: "")
    }
}

struct Text: Drawable {
    var content: String
    init(_ content: String) {
        self.content = content
    }
    func draw() -> String {
        return content
    }
}

struct Space: Drawable {
    func draw() -> String {
        return  " "
    }
}

struct Star: Drawable {
    var length: Int
    func draw() -> String {
        return String(repeating: "*", count: length)
    }
}

struct AllCaps: Drawable {
    var content: Drawable
    func draw() -> String {
        return content.draw().uppercased()
    }
}

let name: String? = "Mike"
let drawing = Line(elements: [
    Star(length: 3),
    Text("Hello"),
    Space(),
    AllCaps(content: Text((name ?? "World") + "!")),
    Star(length: 3)
])

print(drawing.draw())  // ***Hello MIKE!***
```

The content in the `AllCaps` is awkward, **if the content is complex**, and have some extra conditions, it’s hard to write.

So we use the **result builder** to solve this problem.

```swift
@resultBuilder 
struct DrawingBuilder {
    // It combines several components into a line
    static func buildBlock(_ components: Drawable...) -> Drawable{
        return Line(elements: components)
    }
    
    static func buildEither(first: Drawable) -> Drawable{
        return first
    }
    
    static func buildEither(second: Drawable) -> Drawable {
        return second
    }
    
}

func draw(@DrawingBuilder content: () -> Drawable) -> Drawable {
    return content()
}

func caps(@DrawingBuilder content: () -> Drawable) -> Drawable {
    return AllCaps(content: content())
}

func makeGreeting(for name: String? = nil) -> Drawable {
    let greeting = draw {
        Star(length: 3)
        Text("Hello")
        Space()
        caps {
            if let name = name {
                Text(name + "!")
            } else {
                Text("World!")
            }
        }
        Star(length: 2)
    }
    return  greeting
}

let p = makeGreeting()
print(p.draw())        // ***Hello WORLD!**
let s = makeGreeting(for: "Amy")
print(s.draw())        // ***Hello AMY!**
```

The Swift will transform the call to caps(_: ) into code like this:

```swift
let capsDrawing = caps {
    let partialDrawing: Drawable
    if let name = name {
        let text = Text(name + "!")
        partialDrawing = DrawingBuilder.buildEither(first: text)
    } else {
        let text = Text("World!")
        partialDrawing = DrawingBuilder.buildEither(second: text)
    }
    return partialDrawing
}
```

Add support for writing `for` loops

```swift
extension DrawingBuilder {
    static func buildArray(_ components: [Drawable]) -> Drawable {
        return Line(elements: components)
    }
}

let manyStars = draw {
    Text("Stars:")
    for length in 1...3 {
        Space()
        Stars(length: length)
    }
}
```

The result builder makes it easier and neater to organize the function blocks.