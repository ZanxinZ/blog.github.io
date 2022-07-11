---
title: "Closures"
date: 2022-04-02T16:10:40+08:00
summary: The closures can cantains something by the curly bracket.
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---

Closures **are self-contained blocks of functionality that can be passed around and used in your code.

Global and nested functions are special cases of closures.

- Global functions are closures that have name and don’t capture any value.
- Nested functions are closures that have a name and can capture values from the enclosing function.
- Closure expressions are unnamed closures written in a lightweight syntax that can capture values from their surrounding context.

The `sort()` will sort at original array, and `sorted()` will sort and return a new sorted array.

### Usage

- Pass a closure to a function as a tool to do something.
    
    For example, pass as a comparator to a sorted function.
    
- Pass multiple closure to a function as some handler to deal the different circumstances after the function call.
    
    For example, pass a `completion` and `onFailure` closure to a `loadPicture` function, and to do reaction for the picture load complete or fail.
    

---

## Closures’ Syntax

```swift
{(parameters) -> 
		return type in 
		statements, the closure's body
}
```

Pass a function closure to a function, as a tool to do some work.

```swift
func compare(_ s1: String, _ s2: String) -> Bool{
		return s1 > s2
}

let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
var sortedNames = names.sorted(by: compare)  // Here we pass a comparator to the sorted method.
// Here the sortedNames is ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
```

An easy way to pass a function (or say a comparator) to a sorted method.

This is the **inline closure**.

```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
var sortedNames = names.sorted({(_ s1: String, _ s2: String) -> 
		Bool in 
		return s1 > s2
})
```

Inferring Type From Context

```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
var sortedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )
// The compiler will infer the parameter types and the return value type.
```

Implicit Return in Single-Expression Closures

```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
var sortedNames = names.sorted(by: { s1, s2 in s1 > s2 } )
```

Shorthand Argument Names for Inline Closures

```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
var sortedNames = names.sorted(by: { $0 > $1 })  // '$0' means that getting the first parameter to do the comparation..
```

Operator Methods

- `String` type has its string-specific implementation of the grater than operator ‘>’ can be a method that has two parameters of type `String`, and return a value of type `Bool`.

```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
var sortedNames = names.sorted(by: >)
```

## Trailing Closures

Without Trailing Closures

```swift
func someFunction(closure: () -> void) {

}

someFunction(closure: {
    // closure's body, to do something.
})

var sortedNames = names.sorted(by: {$0 >$1})
```

With Trailing Closures

```swift
someFunction() { // Trailing closure's body}

var sortedNames = names.sorted() {$0 > $1}
```

With Trailing Closures Omitting Parentheses

```swift
var sortedNames = names.sorted {$0 > $1}
```

### Apply a Provided Closure to Each Element of A Map

 Using the map() method, and give a closure to it. (The map method here omit the parentheses)

The closure say that input a `Int` , output a `String` ,  the process of the closure is after the `in` . 

```swift
let digitNames = [
    0: "Zero", 1: "One", 2: "Two",   3: "Three", 4: "Four",
    5: "Five", 6: "Six", 7: "Seven", 8: "Eight", 9: "Nine"
]  // A dictionary.
let numbers = [16, 58, 510]

// The type of number can be omitted, because it can be inferred from the value the map to be mapped.
let numbersStr = numbers.map { (number: Int) -> String in 
    var num = number // The 'number' parameter can't be modified, so we set a 'num' to use its value.
    var str: String = ""
    repeat {
        str = digitNames[num % 10]! + str // remainder operate to get the num's last digit.
        num /= 10
    } while num > 0
    return str
}
// Now the numbersStr is ["OneSix", "FiveEight","FiveOneZero"]
```

In the above, `digitNames[num % 10]!` use the exclamation point, because the result from `digitNames` is optional, it may be not found by `num%10` .(The exclamation point is used to force-unwrap the `String` value)

### Function Use Multiple Closures

```swift
func loadPicture(from server: Server, completion: (Picture) -> Void, onFailure: () -> Void) {
		if let picture = download("photo.jpg", from: server) {
				completion(picture)
		} else {
				onFailure()
		}
}
```

When we call the `loadPicture` function, we should give it a `completion` and an `onFailure` closure as two handlers, so that after the network service done the work, with the handler, we can do something when the download complete or fail.

## Capturing Values

A closure can capture constants and variables from the surrounding context in which it’s define. It can **read** those constants and **modify** those variables, even if the original scope no longer exist.

- Nested function is a form of closure that can capture values from it outside scope.
    
    ```swift
    func run(count: Int) -> () -> Int{
        var a = 5
        func wait() -> Int {
    				// The function 'wait' capture the reference of 'a' and 'count' from the surrounding function.
            a += count
            return a
        }
    		func go() -> Int {
    				a += count * 2
    				return a
    		}
        return  wait // It returns a function, not a simple value.
    }
    
    print(run(count: 4)())
    // Print: 9
    // Capturing by reference ensures that 'a' and 'count' don’t disappear when the call to 'run' ends, and also ensures that 'a' is available the next time the 'wait' function is called.
    // If the value isn't mutated by a closure, Swift may store a copy of this value instead of capture it.
    let fun = run(count: 10)
    print(fun())
    print(fun())
    ```
    
    Result
    
    9
    15
    25
    
    The first time use the `run(count: 4)` , it has a scope for run.
    
    The second time use the `run(count: 10)`, it has another scope.
    
    > NOTE
    If you assign a closure to a property of a class instance, and the closure captures that instance by referring to the instance or its members, you will create a strong reference cycle between the closure and the instance. Swift uses *capture lists*
     to break these strong reference cycles.
    > 

## Closures Are Reference Types

```swift
let fun = run(count: 10)
print(fun())
print(fun())

let myFun = fun
print(myFun())
```

Result

15
25
35

## Escaping Closures

In order to let the closure to be use in the function caller’s scope, or the closure want to use the content in the function caller’s scope, we should specify the closure parameter with `@escaping` , otherwise it will cause the compile-time error.

The escaping indicate that the closure is store in a value that’s defined outside the function.

```swift
var handlers: [() -> Void] = []
func addHandle(handle: @escaping () -> Void) {
    handlers.append(handle)
		let han: () -> Void
		han = handle  // The handle must be specified with @escaping

func run() {
    
}

addHandle(handle: run)
```

The code follow is **not allow**, because the handle has’t been defined(create instance) before the addHandle return.

```swift
func addHandle(handle: () -> Void) -> () -> Void {
    return handle // The closure 'handle' can't be use
}
```

Add the `@escaping` to solve the problem above. It will escape(store in a value outside the function) and then call the reference.

```swift
func addHandle(handle: @escaping () -> Void) -> () -> Void {
    return handle
}
```

### Capturing `self`

Capturing `self` in an escaping closure makes it easy to accidentally create a strong reference cycle.

When we want to use the properties in current instance of the class, must **use `self` explicitly,** or include `self` in the **closure’s capture list**.

Use `self` Explicitly

```swift
class Person {
    var age = 23
    func printAge(){
        self.age = 24
        print(self.age)
    }
    func printAgeOther(age: Int){
        print(self.age)}  // Here the self is needed certainly. Otherwise we can't derferentiate the 'age' of the instance and current function.
}
var person = Person()
person.printAgeOther(age: 4)
```

Include the `self` in Capture List

```swift
func doSomething(_ fun: () -> Void){
    fun()
}
class Person {
    var age = 23
    func printAgeOther(){ 
        [self]          // The capture list.
        age = 22
        print(age)      // Here is use the 'self.age'
    }
    
    func myFunc() {
        doSomething(){
            [self] in 
            print(age) // Here is use the 'self.age'
        }
    }
}

var person = Person()
person.printAgeOther()
person.myFunc()
```

- Result
    
    22
    22
    
    Capture `self` in `structure` or `enumeration` 
    
    - Can always refer to `self` implicitly.
    - But an escaping closure can’t capture a mutable reference to `self` .
    
    ```swift
    func doSomething(_ fun: () -> Void){
        fun()
    }
    func doSomethingElse(_ fun: @escaping () -> Void){
        fun()
    }
    struct Person {
        var age = 23
        mutating func myFunc(){
            doSomething(){
                age = 18
                print(age)
            }
    
    // The follow code, 'doSomethingElse' function's parameter is escaping, it will trigger compiler error.
    //        doSomethingElse(){
    //            age = 17
    //            print(age)
    //        }
        }
    }
    
    var person = Person()
    person.myFunc()
    ```
    
    ## Autoclosures
    
    When the closure is assigned to a `var` or a `let` , it hasn’t been call, just give its function reference to other.
    
    It can be use as delays evaluation or overtime judge.
    
    ```swift
    var names = ["Mike", "Alice", "Jhon"]
    print(names.count)
    let theRemover = { names.remove(at: 0)}   // Get the closure's reference.
    print(names.count)
    print("Remove element: \(theRemover())")  // Call the closure, run the closure's body first time.
    print(names.count)
    ```
    
    - Result
        
        3
        3
        Remove element: Mike
        2
        
        `theRemover` has a type `() -> String`
        
    
    Also, we can write it in function like below.
    
    ```swift
    var names = ["Mike", "Alice", "Jhon"]
    print(names.count)
    
    func doSomething(action: () -> String){
        print("Remove elsemet: \(action())")
    }
    
    doSomething(action: {names.remove(at: 0)})
    print(names.count)
    ```
    
    Use the `@autoclosure` , it has the same result as the code above.
    
    ```swift
    var names = ["Mike", "Alice", "Jhon"]
    print(names.count)
    
    func doSomething(action: @autoclosure () -> String){
        print("Remove elsemet: \(action())")
    }
    
    doSomething(action: names.remove(at: 0))
    print(names.count)
    ```
    
    Use the `@autoclosure` and `@escaping` .
    
    ```swift
    var names = ["Mike", "Alice", "Jhon"]
    print(names.count)
    var funs: [() -> String] = []
    
    func doSomething(action: @autoclosure @escaping () -> String){
        print("Remove elsemet: \(action())")
        funs.append(action)  // It need the condition that action is escaping.(maybe because the action is a reference?))
    		name.append("Jane")  // The "Jane" can be added to name.
    }
    
    doSomething(action: names.remove(at: 0))
    print(names.count)
    ```
    
    The escaping means that the argument `action` is allowed to escape the function’s scope.