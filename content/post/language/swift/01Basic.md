---
title: "Basic"
date: 2022-03-12T15:55:40+08:00
summary: Basic usage of the swift syntax.
ShowToc: true
tags: 
- 编程语言
categories: 
- Swift
---

Syntax

```swift
// variable
var number = 6
var title: String?
// const value, should be initialized before use it, can't be changed after its definition
let speed = 200 
let name: String? = nil

// array
var str: [Character] = ["d", "o", "g"]
```

---

## Base data type

| Type | Desc |
| --- | --- |
| UInt8 | UInt8.min is 0, UInt8.max is 255 |
| Int | On the 32-bit platform, it’s as Int32; On the 64-bit platform, it’s as Int64. |
| Uint | On the 32-bit platform, it’s as UInt32; On the 64-bit platform, it’s as UInt64. |
| Double | 64-bit floating-point number |
| Float | 32-bit floating-point number |
| Boolean | true or false |

Numeric 

```swift
/* Numeric Literals */
// They are the same number, in another word, their values are the same. 
let decimalInteger = 17
let binaryInteger = 0b10001
let octalInteger = 0o21
let hexadecimalInteger = 0x11

// Optional exponent (exp)
1.25e2
1.25e-2

// pad with extra zeros and underscores to make them easier to read.
let paddedDouble = 000123.456
let oneMillion = 1_000_000
let justOverOneBillion = 1_000_000.000_000_1

/* Numeric Type Conversion */
// Type automatic convert
let a: UInt16 = 2000
let b: UInt8 = 1    // Different type can't be sum together
let c = a + b        // Here, system use b to create a new UInt16 object, so this new object can sum with a.

// Type force convert
let three = 3
let num = 0.14159
let pi = Double(three) + num  // conversions between integer and floating-point numeric types must be made explict.

// Truncate
let integerPi = Int(pi)

// The rules for combining numeric constants and variable are different from the rules for numeric literals.
// Number literals don’t have an explicit type in and of themselves.
```

Type Aliases

```swift
typealias MyInt16 = UInt16

var num: MyInt16 = MyInt16.min  // it's actually calls the UInt16.min
```

Tuples

```swift
let http404Error = (404, " Not Found")          // compose a tuple 

let (statusCode, statusMessage) = http404Error  // decompose a tuple

let (justTheStatusCode, _) = http404Error       // use a underscore(_) to ignore other values

let statusCodeByIndex = http404Error.0          // get the tuple's individual element value by index, start at 0

// name the tuple's individual element
let http200Status = (statusCode : 200, description : "OK")

let status200 = http200Status.statusCode
let description200 = http200Status.description
```

Optional : **variable can be a value or nil.** Just add a `?` after the variable’s  type.  `nil`meaning “the absence of a valid object.”

only optional type can be `nil`.

const value should not be `nil`, it has no specific meaning in application.

```swift
var name: String? = "Hello"

// Because the initializer might be fail, it returns an optional Int (Int?), rather than an Int.     
let num = Int("123")

var answer: Int?   // the answer is automatically set to nil.

// In Objective-C, nil is a pointer to a nonexistent object. 
// In Swift, nil isn’t a pointer—it’s the absence of a value of a certain type.

// Include multiple condition
// If any of the values in the optional bindings are nil or any Boolean condition evaluates to false, the whole if statement’s condition is considered to be false.
if let firstNum = Int("4"), let secondNum = Int("5"), firstNum < secondNum && secondNum < 100 {
		print("\(firstNum) < \(secondNum) < 100")
}

let hobby: String?        // ordinary optional
let myHobby = hobby!       // must require a exclaimation point to unwrap the object.

// An implicitly unwrapped optional as giving permission for the optional to be force-unwrapped if needed
let address: String!      // implicitly option. the name can be accessed when it is nil and without exclaimation point.
let hisAddress = address   // it is permitted, no need for an exclaimation point.

// we are recommanded to use the ordinary optional in development.
// because the implicitly optional can easily be out of control.

// ######## error #########
// this will trigger a runtime error
var str: String! = nil
print(str.count)
// this will trigger a runtime error
var bag: String?
print(str!.count). // 'str!' is nil, so it can't be accessed. 

// this will trigger a compiler error
var pack: String?
print(str.count)  // str is an ordinary option, need an unwrap(!).
// #######################
```

---

## Function’s argument and its label

- use parameter label
    
    ```swift
    func addTwo(arg1 a : Int, arg2 b : Int) -> Int {
      let c = a + b
      return c
    }
    let sum = addTwo(arg1: 4, arg2: 28)
    ```
    
- Tell parameter’s name apparently(Recommended when the method has multi-parameter)
    
    ```swift
    func addTwo(a : Int, b : Int) -> Int {
      let c = a + _b
      return c
    }
    let sum = addTwo(a: 4, b: 28)
    ```
    
- use [ _ ]  to pass over the label (Recommended when the method has only one parameter)
    
    ```swift
    func addTwo(_ a : Int, _ b : Int) -> Int {
      let c = a + b
      return c
    }
    let sum = addTwo(4, 28)
    ```
    
- Function’s define and invoke no need to be sequenced. It can be invoked before its define.
    
    ```swift
    // invoke function 
    goToSchool(
    //function definition
    func goToSchool() {
        print("I go to school.")
    }
    ```
    

---

## Object Oriented

Class

```swift
class Person {
    var name = "neo"
    var age = 0
    
    init () {}
    init (_ name: String, _ age: Int) {
        self.name = name
        self.age = age
    }
}
// use different init function to create object.
var a = Person()
var b = Person("Mike", 26)
print(a.name + " " + String(a.age))
print(b.name + " " + String(b.age))
```

Optional

```swift
class BlogPost {
    // title can be String or nil
    var title : String?
    
}
let post = BlogPost()
print(post.body + " hello!")

//post.title = "yo"

// Optional Binding
if let actualTitle = post.title {
    print(actualTitle + " Hey")
}
else {
		print("post.title is \(post.title)")
}

// Testting for nil
if post.title != nil {
    print(post.title! + " not nil") // exclaimation '!' means force unbox 
}
if post.title == nil {
    print("It's nil")
}

// Implicity optional
var name: String! = "Amy" // '!' means that implicity optional
if name != nil {
		// no need to use '!'
		print(name + " not nil")
}
```

Initializer

- The rule of initializer
    1. A designated initializer must call the designated initializer from the immediately superclass.
        
        ```swift
        class Worker: Person {
            override init () {
                super.init()
                print("subclass's designated initializer")
            }
        		// it has no conflict with the superclass's convenience method.  
            init (customName: String, age: Int) {
        				// designated initializer call the designated initializer from superclass.
        				super.init(name: customName)
        				self.age = age
                print("subclass's designated initializer with two parameters")
            }
        }
        
        class Person {
            var name = "neo"
            var age = 0
            
            init () {
                age = 3
                name = "ha"
            }
        		init (name: String) {
                self.name = name
            }
        		convenience init (customName: String, age: Int) {
                self.init(name: customName)
                self.age = age
            }
        }
        ```
        
    2. A convenience initializer must call another designated initializer from the same class.
    3. A convenience initializer must ultimately call a designated initializer.
- designated initializer
    
    ```swift
    init () {
            age = 3
            name = "ha"
        }
    ```
    
    ```swift
    init(_ name: String, _ age: Int) {
    		self.name = name
    		self.age = age
    }
    ```
    
- convenience initializer
    
    if we want to invoke the designated initializer **self.init()** in a newly init method, we should add a keyword ‘convenience’ to decorate this newly method. (**The reason why we use the ‘convenience’ keyword**)
    
    ```swift
    convenience init(name: String) {
    		self.init()     // rule 3 of the rules we talk before.
        self.name = name
    }
    ```
    
- Inheritance
    
    Swift only permit single inheritance
    
    ```swift
    class Car {
        var topSpeed = 200
        func drive() {
            print("Drive at \(topSpeed)")
        }
    }
    
    // inheritance
    class FutureCar: Car() {
    		// the method belong to current class
    		func fly() {
            print("Fly at \(topSpeed + 20)")
    		}
    }
    let myRide = Car() // create an instance from the class 'Car'.
    myRide.drive()
    let hisCar = FutureCar()
    hisCar.fly()       // method create by the subclass.
    ```
    
- Polymorphism
    - Override
        
        The method has the same name but the arguments’ type or count are different.
        
        If we call with different types or count of arguments , the phenomenons(results) are different.
        
        ```swift
        class Car {
            var speed = 200
            func drive() {
                print("Drive at \(speed)") // the 'speed' can be use in this class 'Car'
            }
            func drive(_ speed: Int) {
                print("Drive at \(speed)") // the 'speed' can be only used in this func
            }
        }
        let myCar = Car()
        myCar.drive()
        myCar.drive(160)
        ```
        
        ```swift
        // another case
        goToSchool()
        goToSchool("Mike")
        goToSchool("Micle", "Jhon")
        goToSchool(15)
        func goToSchool() {
            print("I go to school.")
        }
        func goToSchool(_ peoleA: String) {
            print("I go to school with \(peoleA).")
        }
        func goToSchool(_ peopleA: String, _ peopleB: String) {
            print("I go to school with " + peopleA + " and " + peopleB + ".")
        }
        func goToSchool(_ speed: Int) {
            print("I go to school in a speed of \(speed).")
        }
        ```
        
        The subclass define a same method already in the superclass, but the implement of this method is different from that implemented in the superclass. Use the key word ‘override’
        
        ```swift
        class Car {
            var speed = 200
            func drive() {
                print("Drive at \(speed)")
            }
        }
        
        // inheritance
        class FutureCar: Car() {
            // override the method from the superclass.
        		override func drive() {
        			print("Drive at \(speed + 5)")
        		}
        }
        let myRide = Car()//create an instance from the class 'Car'.
        myRide.drive()
        let hisCar = FutureCar()
        hisCar.drive()    //method override by the subclass.
        ```
        
    

## Error Handling

Two way to solve the error **on runtime**

- throws
    
    ```swift
    func doSomething() throws {
    	// Here may or may not throw an error
    }
    ```
    
- do try catch
    
    do the right things to face the error.
    
    ```swift
    do {
    	try makeSandwich()
    	// no error was thrown
    	eatSandwich()
    } catch SandwichError.outOfCleanDishes {
    	// an error was thrown
      washDish()
    } catch SandwichError.missingIngredients(let ingredients) {
    	// an error was thrown
      buyGroceries(ingredients)
    } catch {
    	// catch other errors
    }
    ```
    
    ## Assertion & Precondition
    
    A assertion or precondition indicates an invalid program state, there’s no way to catch a failed assertion.
    
    ```swift
    // Assertion
    let age = -3
    assert(age >= 0, "Age can't be less than 0") // (the condition, the message to be shown when the condition is false)
    assert(age >= 0)      // omit the message, it's ok.
    if age < 0 {
    		assertionFailure("Age can't be less than 0")  // when the code already check the failure, use the assertionFailure to show message directly.
    }
    
    // Precondition
    let index = -1
    precondition(index >= 0, "Index must be greater than 0")
    precondition(index >= 0)
    
    preconditionFailure("Index must be greater than 0")
    // If you compile in unchecked mode (-Ounchecked), preconditions aren’t checked.But the assertion still check.
    // Assertion check in debug environment, not check in realease environment.
    // Precondition check in debug environment and release environment.
    
    // Use fatalError(_ :file:line:) to halt execution
    // During prototyping and early development to create stubs for functionality that hasn’t been implemented yet, write a fatalError as the stub implementation.
    // if the code use the functionality which hasn't been implemented, that will result in a fatal error. It will remind us to implement the function or method.
    fatalError("Unimplemented") 
    ```