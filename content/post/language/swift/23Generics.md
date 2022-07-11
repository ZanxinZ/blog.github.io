---
title: "Generics"
date: 2022-06-05T16:00:40+08:00
ShowToc: true
summary: Define a class with template type <T>, then should illustract the type when use the class.
tags:
- 编程语言
categories:
- Swift
---

Write code in a more abstract way. It make the code flexible and more reusable.

`Array` and `Dictionary` types are both generic collections.

## The Problem That Generics Solve

If the code with no generics:

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let tmp = a
    a = b
    b = tmp
}

func swapTwoStrings(_ a: inout String, _ b: inout String) {
    let tmp = a
    a = b
    b = tmp
}

func swapTwoDoubles(_ a: inout Double, _ b: inout Double) {
    let tmp = a
    a = b
    b = tmp
}

// Use the function
var aInt = 3, bInt = 4
var aString = "Mike", bString = "Amy"
var aDouble = 1.1, bDouble = 2.5

swapTwoInts(&aInt, &bInt)
swapTwoStrings(&aString, &bString)
swapTwoDoubles(&aDouble, &bDouble)

print(aInt, bInt)
print(aString, bString)
print(aDouble, bDouble)

// Print:
// 4 3
// Amy Mike
// 2.5 1.1
```

Once the code use generics:

```swift
// The <T> means that this function will use a generic and the type is T.
func swapTwoObj<T>(_ a: inout T, _ b: inout T) {
    let tmp = a
    a = b
    b = tmp
}

// Use the function
var aInt = 3, bInt = 4
var aString = "Mike", bString = "Amy"
var aDouble = 1.1, bDouble = 2.5

swapTwoObj(&aInt, &bInt)
swapTwoObj(&aString, &bString)
swapTwoObj(&aDouble, &bDouble)

print(aInt, bInt)
print(aString, bString)
print(aDouble, bDouble)

// Print:
// 4 3
// Amy Mike
// 2.5 1.1
```

So the generics can clearly reduce the duplicated code when the code do the similar things.

## Type Parameters

We can define more than one generic types in the angle brackets.

```swift
func swapTwoObj<T, C, D>(_ a: inout T, _ b: inout T, _ c: C, _ d: D) {
    let tmp = a
    a = b
    b = tmp
}
```

## Generic Types

The generics can be applied to classes, structures and enumerations.

An example that write a generic `stack` : First In First Out

```swift
class Stack<T> {
    var store: [T] = []
    func push(_ element: T) {
        store.append(element)
    }
    
    func pop() -> T? {
        if store.count > 0 {
            var t = store[store.count - 1]
            store.remove(at: store.count - 1)
            return t
        }
        return nil
    }
    
    func isEmpty() -> Bool {
        return store.count <= 0
    }
}

let stack = Stack<Int>()
stack.push(4)
stack.push(22)
stack.push(6)

while !stack.isEmpty() {
    if let e = stack.pop() {
        print(e)
    } else {
        print("e is nil")
    }
}

// Print: 
// 6
// 22
// 4
```

## Extending a Genetic Type

```swift
extension Stack {
    var topItem: T? {
        return store.isEmpty ? nil : store[store.count - 1]
    }
}
stack.push(5)
stack.push(8)
if let top = stack.topItem {
    print(top) // 8
}
```

## Type Constraints

Here we constrain the genetic types that they should conform to the specific protocol.

```swift
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // function body goes here
}
```

Example:

The `T: Equatable`  define that the T must conform to the Equatable protocol so that the T can be compared with `==`.

```swift
func findIndex<T: Equatable> (of target: T, in array: [T]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == target {
            return index
        }
    }
    return  nil
}

let strings = ["cat", "dog", "llama", "parakeet", "terrapin"]

if let foundIndex = findIndex(of: "dog", in: strings) {
    print("Index: \(foundIndex)")
}

// Print:
// Index: 1
```

## Associated Type

The actual type to use for that associated type isn’t specified until the protocol is adopted.

```swift
// The conforming type must provide these three requirements.
protocol Container {
    associatedtype Item   // The actual type will be infered when the append method is implemented.
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

The IntStack implementation

```swift
struct IntStack: Container {
    var items: [Int] = []
    // It demonstrates the associatedtype.
    typealias Item = Int
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    mutating func append(_ item: Int) {
        push(item)
    }
    
    var count: Int {
        items.count
    }
    
    subscript(i: Int) -> Int {
        items[i]
    }
    
}
```

The generic stack conform to the container.

```swift
struct Stack<Element>: Container {
    var items: [Element] = []
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element{
        return items.removeLast()
    }
    
    // It implements the appen(_: ) requirement, Swift can therefore infer that Element is the associatedtype for the contaioner.
    mutating func append(_ item: Element) {
        push(item)
    }
    var count: Int {
        items.count
    }
    subscript(i: Int) -> Element {
        return items[i]
    }
}
```

### Extending an Existing Type to Specify an Associated Type

Swift’s `Array` type already provides `append(_: )`, `count`, `subscript`. This means that we can extend `Array` to conform the Container protocol.

```swift
extension Array: Container {}
```

### Adding Constraints to an Associated Type

```swift
protocol Container {
    associatedtype Item: Equatable // Here the Item conform to the Equatable is a constraints
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

### Using a Protocol in its Associated Type’s Constraints

Here using the custom protocol `SuffixableContainer` in  its constraints.

```swift
protocol SuffixableContainer: Container {
    // It defines an associatedtype named Suffix and its Item type must be the same as the container's Item type.
    associatedtype Suffix: SuffixableContainer *where* Suffix.Item == Item  // Use　the protocol suffixableContainer to constrain the associated type. 
    func suffix(_ size: Int) -> Suffix
}

// The extension 
extension Stack: SuffixableContainer {
    // It implement the method, and Swift infer that Suffix is Stack.
    func  suffix(_ size: Int) -> Stack{
        var result = Stack()
        for index in (count - size)..<count {
            result.append(self[index])
        }
        return result
    }
}

var stackOfInt = Stack<Int>()
stackOfInt.push(3)
stackOfInt.push(6)
stackOfInt.push(9)
let suffix = stackOfInt.suffix(2)
print(suffix)

// Print:
// Stack<Int>(items: [6, 9])
```

## Generic Where Clauses

A generic `where` clause require that an associated type must conform to a certain protocol, or that the certain type parameters and associated types must be the same.

```swift
func allItemMatch<C1: Container, C2: Container>(_ container: C1, _ anotherContainer: C2) -> Bool where C1.Item == C2.Item, C1.Item: Equatable {
    // Check if two container's count equal.
    if container.count != anotherContainer.count {
        return false
    }
    // Check each item pairs to see if they are equivalent.
    for i in 0..<container.count {
        if container[i] != anotherContainer[i] {
            return false
        }
    }
    
    return true
}
```

We already extends the array with the code **`extension** Array: Container {}` before, now both the `Stack<String>` and `Array`  are conform to the `Container`, so they can be the parameter for the method `allItemMatch`.

```swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tree")

var arrayOfStrings = ["uno", "dos", "tree"]

// Now C1 is Stack<String> and C2 is Array
if allItemMatch(stackOfStrings, arrayOfStrings) {
    print("Matched")
} else {
    print("Not match")
}
```

## Extension with a Generic Where Clause

It means that the extension have some condition to be valid. If the stack whose elements aren’t equatable and it try to call the `isTop(_ :)` , it  will trigger a compile-time error.

```swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tree")

// The extension only be vaild where the element conform to the Equatable protocol.
extension Stack where Element: Equatable {
    func isTop(_ item: Element) -> Bool {
        guard let topItem = items.last else {
            return false
        }
        return topItem == item
    }
}

print(stackOfStrings.isTop("tree"))
```

We can also use generic `where` clause with extension to a protocol.

```swift
extension Container where Item: Equatable {
    func  startWith(_ item: Item) -> Bool {
        return count >= 1 && self[0] == item
    }
}
```

We can also constrain the extension’s generic type must be a specific type. Only when the condition is satisfied, the extension can be valid.

```swift
extension Container where Item == Double {
    func average() -> Double {
        var sum = 0.0
        for i in 0..<count {
            sum += self[i]
        }
        return  sum / Double(count)
    }
    
}

print([11.2, 5.3, 3.2].average()) // 6.566666666666666
```

## Contextual Where Clauses

Use the condition to constrain only the method. These method only be available when the condition is satisfied.

```swift
extension Container {
    func average() -> Double where Item == Int {
        var sum = 0.0
        for i in 0..<count {
            sum += Double(self[i])
        }
        return  sum / Double(count)
    }
    
    func endsWith(_ item: Item) -> Bool where Item: Equatable{
        return count >= 1 && self[count - 1] == item
    }
}
```

The same behavior implemented in another way. Moving those requirement in different extensions.

```swift
extension Container where Item == Int {
    func average() -> Double {
        var sum = 0.0
        for index in 0..<count {
            sum += Double(self[index])
        }
        return sum / Double(count)
    }
}
extension Container where Item: Equatable {
    func endsWith(_ item: Item) -> Bool {
        return count >= 1 && self[count-1] == item
    }
}
```

Two way to do the `where` clause above have the same behavior. They activate the specific requirements when the condition is satisfied. However, the contextual `where` clause only need one extension, and the extensions’ generic `where` clause will require one extension for per requirement.

## Associated Type with a Generic Where Clause

Here we define the `Iterator` conform to the `IteratorProtocol` where the type `Iterator.Element` is the same as `Item`.

```swift
protocol  Container {
    associatedtype Item
    mutating func append(_ item: Item) 
    var count: Int { get }
    subscript(i: Int) -> Item { get }
    associatedtype Iterator: IteratorProtocol where Iterator.Element == Item
    func makeIterator() -> Iterator
}
```

We can add a constraint to an inherited associated type by including the generic `where`
 clause in the protocol declaration.

```swift
protocol  ComparableContainer: Container where Item: Comparable {}
```

## Generic Subscripts

Use the `where` to constrain.

```swift
extension Container {
    subscript<Indices: Sequence>(indices: Indices) -> [Item] where Indices.Iterator.Element == Int{
        var result: [Item] = []
        for i in indices {
            result.append(self[i])
        }
        return  result
    }
}
```