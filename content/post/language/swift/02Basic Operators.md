---
title: "Basic Operators"
date: 2022-03-19T16:00:40+08:00
summary: Basic usage of the operators.
ShowToc: true
tags: 
- 编程语言
categories: 
- Swift
---


## Terminology

- Unary operator (such as -a)
- Binary operator(such as 2 + 3)
- Ternary operator(such as a ? b : c)

The values that operators affect are operands. Prefix, infix, suffix

Assignment Operator

```swift
// Assignment
let b = 10
var a = 5
a = b

// tuple assignment
let (x, y) = (1, 2)

// not allowed, it is to prevent miss use when the equal operator(==) is actually intended.
if x = y {
	// This isn't valid, because x = y doesn't return a value.
}
```

Arithmetic Operators

| Addition | + |
| --- | --- |
| Subtraction | - |
| Multiplication | * |
| Division | / |

```swift
1 + 1
5 - 3
2 * 3
10.0 / 2.5

"hello, " + "world"   // Addition operator is also supported for String concatenation 
```

Remainder Operator

b = (a x some multiplier) + remainder

```swift
// 9 = 4 + 4 + 1
9 / 4   // it equals to 2
9 % 4   // it equals to 1

// Calculate method
// a % b 
// b = (a x some multiplier) + remainder
-9 % 4  // it equals to -1
// -9 = (4 x -2) + -1

// The sign of 'b' is ignored.
// a % b and a % -b always give the same answer.
```

Compound Assignment Operators

```swift
a += 2   // it equals a = a + 2
b -= 3
c *= 2
d /= 2

// The compound assignment operators don't return a value. 
// let b = a += 2  is illegal
```

Comparison Operator

| Equal to | a == b |
| --- | --- |
| Not equal to | a != b |
| Greater than | a > b |
| Less than | a < b |
| Greater than or equal to | a >= b |
| Less than or equal to | a <= b |

```swift
// Compare the tuples
// from left to right
(1, "zebra") < (2, "apple")  // true
(3, "apple") < (3, "bird")   // true
(4, "dog") == (4, "dog")     // true

// The element in the tuple should be comparable, so that the tuple can be comparable
(String, Int) < (String, Int)  // It's ok
(String, Boolean) < (String, Int) // Error, Boolean can't be compared
```

Ternary Operator

```swift
var a = 3
var b = 4
c = a > b ? 2 : 1

// The code equals to
var a = 3
var b = 4
if a > b {
	c = 2
} else {
	c = 1
}
```

## Nil-Coalescing Operator

Only optional type can use nil-coalescing operator

```swift

// unwrap an optional 'a' if it contains a value, or return a default value 'b' if 'a' is nil.
a ?? b

// The code equals to
a != nil ? a! : b

// Usage
let defaultColorName = "red"
var userDefinedColorName : String?  // It's assign with nil in default.

var colorNameToUse = userDefinedColorName ?? defaultColorName
// it's red

userDefinedColorName = "Green"
colorNameToUse = userDefinedColorName ?? defaultColorName
// it's green
```

## Range Operator

Closed Range Operator

```swift
// From 1 to 5, include 1 and 5
for i in 1...5 {
	
}
```

Half-Open Range Operator

```swift
// From 1 to 5, include 1 but not 5
for i in 1..<5 {
	
}
// it's useful in array traversal
```

One-Side Range

```swift
for name in names[2...] {
	// from 2 to the end, include 2
}

for name in names[...2] {
	// from 0 to 2, include 2
}

for name in names[..<2] {
	// from 0 to 2, not include 2
}

// One-side operator can be used in other contexts, not just in subscripts.
let range = ...5
range.contains(7)
range.contains(5)
range.cantains(-3)
```

## Logical Operator

| Logical NOT | !a |
| --- | --- |
| Logical AND | a && b |
| Logical OR  | a || b |

Combining Logical Operators

The compound expression are left-associative.

```swift
if enteredDoorCode && passedRetinaScan || hasDoorKey || knowsOverridePassword {
	print("Welcome!")
} else {
	print("Access Denied")
}
```

## Explicit Parentheses

Add a pair of parentheses to make it easier to read.s

```swift
if (enteredDoorCode && passedRetinaScan) || hasDoorKey || knowsOverridePassword {
	print("Welcome!")
} else {
	print("Access Denied")
}
```