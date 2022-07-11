---
title: "Error Handling"
date: 2022-05-07T16:00:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---

A class support for throwing, catching, propagating, and manipulating recoverable errors at *runtime*.

When an optional fails, it’s useful to understand what cause the failure, so that the code can respond accordingly.

## Example:

Reading a file from the disk may be fail in some way.

- File not exist
- Have no permission to read.
- File not being encoded in a compatible format.

Distinguishing among these different situations allows a program to resolve some errors and to communicate to the user any errors it can’t resolve.

## Representing and Throwing Errors

We can custom an error enumeration, and conform this enumeration to the `Error` protocol, so that the custom error can be use as a standard error.

```swift
// Represent
enum VendingMachineError: Error {
    case invaildSelection
    case insufficientFunds(coinsNeeded: Int)
    case outOfStack
}

struct Item {
    var price: Int
    var count: Int
}

class VendingMachine {
    var inventory = [
        "Cola": Item(price: 3, count: 10),
        "Cake": Item(price: 12, count: 11),
        "Chips": Item(price: 8, count: 5)
    ]
    var coinsDeposited = 0
    func vend(name: String) throws {
        guard let item = inventory[name] else {
            throw VendingMachineError.invaildSelection
        }
        guard item.price <= coinsDeposited else {
            throw VendingMachineError.insufficientFunds(coinsNeeded: item.price - coinsDeposited)
        }
        guard  item.count >= 0 else {
            throw VendingMachineError.outOfStack
        }
        coinsDeposited -= item.price
        inventory[name]?.count -= 1
        print("Dispensings \(name)")
    }
}
```

Testing the error throwing.

```swift
var machine = VendingMachine()

do {
    machine.coinsDeposited = 15
    try machine.vend(name: "Cake")
    print("Vend cake succeed")      // Current coinsDeposited is 6.
    try machine.vend(name: "Chips") 
    print("Vend chips succeed")
} catch  VendingMachineError.insufficientFunds, VendingMachineError.invaildSelection, VendingMachineError.outOfStack {
    print("Have an error")
}

// Print:
// Dispensings Cake
// Vend cake succeed
// Have an error
```

The `do{ try }` will be interrupted if the some errors were thrown.

## Handling Errors

Four way to handle error.

- Propagate the error to whom call the function.(Pass the error to higher level)
- Use `do-catch` statement.
- Handle the error as an optional value. (`try?`)
- Assert that the error will not occur. (`try!`)

Swift doesn’t involve unwinding the call stack in which can be computationally expensive.

### 1. Propagate Error Using Throwing

```swift
let people = ["Amy": "Cake", "Bob": "Chips"]
func buyFavoriteSnack(name: String) throws {
    try machine.vend(name: name)
}
```

### 2. Using Do-Catch

```swift
do {
		try something
} catch parameter1 {

} catch parameter2 where condition {

}
```

Example

```swift

do {
    try machine.vend(name: "cola")
} catch is Error {
    // Catch all errors
    print("Transation fail.")
}

do {
    try machine.vend(name: "Cake")
} catch VendingMachineError.insufficientFunds {
    print("insufficientFunds")
} catch {
    // Catch other errors.
}
```

If none of the `catch` handle the error, the error propagates to the surrounding scope. And finally the error must be handled by the top-level scope, otherwise the error will triggers the runtime error.

Finally handle the errors.

```swift
func buySomething(name: String) throws{
    do {
        try machine.vend(name: name)
    } catch VendingMachineError.outOfStack {
        print("Out of stack")
    }
}
do {
    try buySomething(name: "Cake")
} catch VendingMachineError.invaildSelection {
    print("insufficientFunds")
} catch {
    // Catch other errors. Otherwise the other errors will trigger runtime error.
    print("Other")
}

// Print: Other
```

Write in a better form.

```swift
func buySomething(name: String) throws{
    do {
        try machine.vend(name: name)
    } catch is VendingMachineError {
        print("Vending Maching have an error.")
    }
}
```

### 3. Converting Errors to Optional Values

Use `try?` to convert errors to optional value. 

```swift
func remain(name: String) throws -> Int {
    
    if let item = machine.inventory[name] {
        return item.count
    } else {
        throw VendingMachineError.invaildSelection
    }
}

var countOfCake = try? remain(name: "Cake")  // Use the 'try?' to convert the error into a optional value.

var count: Int?
do {
    try count = remain(name: "Cake")
} catch is Error {
    count = nil
}

print(countOfCake)

// The countOfCake is equal to the count. They are all optional.
```

### 4. Assert the Error Not Occur

`try!` return a normal type. If the method/init throws an error, it will crash. Because the returned type will be `nil` and a normal type cannot handle `nil`.

**Disabling Error Propagation**

Use the `try!` to disable error propagation and wrap the call in a runtime assertion

```swift
func remain(name: String) throws -> Int {
    
    if let item = machine.inventory[name] {
        return item.count
    } else {
        throw VendingMachineError.invaildSelection
    }
}

let c = try! remain(name: "Cola") 

let e = try! remain(name: "Bread") // It will raises a runtime error.
```

A*void using `try!` in most cases since it will break your program.*

## Specifying Cleanup Action

Use a `defer` statement to execute a set of statements just before code leaves the current block of code.

A `defer` statement defers execution until the current scope is exited, and it has a backward sequence to call the statements in the defer block.

```swift
func processFile(name: String) throws{
    if exists(name) {
        let file = open(name) 
        defer {
            close(file)       // Executed third
            doSomething() // Executed second
            stop()        // Executed first
        }
        while let line = try file.readline() {
            
        }
        
        // Here call the stop, doSomething, close
    }
}
```