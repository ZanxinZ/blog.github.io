---
title: "Opaque Type"
date: 2022-06-05T16:05:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---


A function with an opaque type hides its return value’s type information. Hiding type information at some boundaries between a module and code that calls into the module. Unlike returning a value whose type is a protocol type, opaque type preserve type identity —the compile has access to the type information, but clients of the module don’t.

## The Situation

Here we have a `Shape` protocol.

```swift
protocol Shape {
    func draw() -> String
}
```

The struct `Triangle` conform to the `Shape`. Describe how to `draw()`.

```swift
struct Triangle: Shape {
    var size: Int
    func draw() -> String {
        var result: [String] = []
        for length in 1...size {
            result.append(String(repeating: "*", count: length))
        }
        return result.joined(separator: "\n")
    }
}

let smallTriangle = Triangle(size: 4)
print(smallTriangle.draw())
// Print:
// *
// **
// ***
// ****
```

The struct `FlippedShape` conform to the `Shape` and it need an injection in a type of `Shape`.

```swift
struct FlippedShape<T: Shape>: Shape{
    var shape: T
    func draw() -> String {
        let lines = shape.draw().split(separator: "\n")
        return lines.reversed().joined(separator: "\n")
    }
}

let filppingShape = FlippedShape<Triangle>(shape: smallTriangle)
print(filppingShape.draw())
// Print:
// ****
// ***
// **
// *
```

The `JoinedShape` conform to the `Shape` and it need two injection in type of `Shape`. It use the generic type `T` and `U`. 

```swift
struct JoinedShape<T: Shape, U: Shape>: Shape{
    var top: T
    var bottom: U
    func draw() -> String {
        return top.draw() + "\n" + bottom.draw()
    }
}
let joinedShape = JoinedShape(top: smallTriangle, bottom: filppingShape)
print(joinedShape.draw())
// Print:
// *
// **
// ***
// ****
// ****
// ***
// **
// *
```

## Returning an Opaque Type

### The opaque type like being the reverse of a generic type.

In generic, the function return a type that depends on its caller:

```swift
func max<T>(_ x: T, _ y: T) -> where T: Comparable {...}
```

Use opaque type. It return a `some` type and don’t exposing the underlying type of that shape. It only focus on the return type, not the specific type.

```swift
struct Square: Shape {
    var size: Int
    func draw() -> String {
        let line = String(repeating: "*", count: size)
        let result = Array<String>(repeating: line, count: size) count: <#T##Int#>)
        return  result.joined(separator: "\n")
    }
}

func makeTrapezoid() -> some Shape {
    let top = Triangle(size: 2)
    let middle = Square(size: 2)
    let bottom = FlippedShape(shape: top)
    let trapezoid = JoinedShape(top: top, bottom: JoinedShape(top: middle, bottom: bottom))
    return trapezoid
}

// Here we get a trapezoid, it's something that conform to the Shape and we can only use it as shape, the client  can't access the underlying information of this shape.
let trapezoid = makeTrapezoid()
print(trapezoid.draw())

// Print:
// *
// **
// **
// **
// **
// *
```

### Combine Opaque Return Type with Generics.

```swift
func flip<T: Shape>(_ shape: T) -> some Shape {
    return FlippedShape(shape: shape)
}
func join<T: Shape, U: Shape>(_ top: T, _ bottom: U) -> some Shape {
    return JoinedShape(top: top, bottom: bottom)
}
let opaqueJoinedTriangle = join(smallTriangle, flip(smallTriangle))
print(opaqueJoinedTriangle.draw())

// Print:
// *
// **
// ***
// ****
// ****
// ***
// **
// *
```

All the possible  opaque return in a function must have the same type.

```swift
// Here is an example in error 

func invalidFlip<T: Shape>(_ shape: T) -> some Shape {
    if shape is Square {
        return shape
    }
    return FlippedShape(shape: shape)
}
```

One way to avoid return different type is to move this `Square` case into the `FlippedShape` implementation.

```swift
struct FlippedShape<T: Shape>: Shape{
    var shape: T
    func draw() -> String {
        if shape is Square {
            return shape.draw()
        }
        
        let lines = shape.draw().split(separator: "\n")
        return lines.reversed().joined(separator: "\n")
    }
}
```

Using generics in an opaque return type.

```swift
func repeatObj<T: Shape>(shape: T, count: Int) -> some Collection {
    return Array<T>(repeating: shape, count: count)
}
```

## Differences Between Opaque Types and Protocol Types

### Using protocol type

It can return different type that conform to `Shape` , it makes a much looser API than opaque return type make. 

```swift
// Protocol type
func protoFlip<T: Shape>(_ shape: T) -> Shape {
    if shape is Square {
        return shape
    }

    return FlippedShape(shape: shape)
}
```

The less specific return type information means that the operation that depends on type information aren’t available on the return value.

```swift
let protoFlipTriangle = protoFlip(smallTriangle)
let sameThing = protoFlip(smallTriangle)
print(protoFlipTriangle == sameThing)   // Error they are 'Shape', 'Shape' has no func to check if they are equal, operator '==' cannot be applied to two 'Shape'  
```

The opaque types preserve the identity of the underlying type.

```swift
protocol Container {
    associatedtype Item 
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}

extension Array: Container {}
```

Here we:

- can’t use `Container` as the return type of a function. Because the protocol has an associated type.
- And can’t use it as constraint in a generic return type. Because there isn’t enough information outside the function body to infer what the generic type needs to be.

```swift
// Error: Protocol with associated types can't be used as a return type.
func makeProtocolContainer<T>(item: T) -> Container {
    return [item]
}

// Error: Not enough information to infer C, it has associate type
func makeProtocolContainer<T, C: Container>(item: T) -> C {
    return [item]
}

```

Using the opaque type `some Container` as a return type. It means that the function return a container, but declines to  specify the container’s type.

```swift
func makeOpaqueContainer<T>(item: T) -> some Container {
    return  [item]
}

let opaqueContainer = makeOpaqueContainer(item: 12)
let twelve = opaqueContainer[0]
print(type(of: twelve))  // Int
```