---
title: "Function"
date: 2022-04-02T16:08:40+08:00
summary: Call the function(method) to do something detail.
ShowToc: true
tags: 
- 编程语言
categories: 
- Swift
---


The function has two way to affect the outside scope

- Use return value to tell the caller.
- Use the `inout` with reference to change the the original value outside.

## Function Usages

Without Parameter and Return Value

```swift
func sayHello() {
		print("Hello")
}

// Call the function, will print「hello」
sayHello()       
```

With Parameter

```swift
func greet(name: String) {
		print("Hello \(name)") 
}

// Call the function pass the String as the input to the function, will print 「Hello Mike」
greet(name: "Mike")    
```

With Multiple Parameter

```swift
func addTwoNum(a: Int, b: Int) {
		var c = a + b
		print(c)
}

// Call the function, pass two parameter to the function, will print 「11」 
addTwoNum(a: 5, b: 6)  
```

With Return Value

once defined the return type of a function, it must have a return operation when the function logic end.

```swift
func addTwoNum(a: Int, b: Int) -> Int {
		var c = a + b
		return c
}

// The res will be assigned as 「12」
var res = addTwoNum(a: 3, b: 9)

// The return value can be ignored.
addTwoNum(a: 4, b: 9)
```

With Multiple Return Value

```swift
// Return as a tuple
func getTwoNum() -> (Int, Int) {
		var a = 2
		var b = 4
		return (a, b)
}

var nums = getTwoNum()

// Return as a optional dictionary 
func getThreeNum() -> (a: Int, b: Int, c: Int)? {
		var a = 2
		var b = 4
		var c = 3
		return (a, b, c)
}
```

With Multiple Parameter and Multiple Return Value

```swift
func minMax(array: Int[]) -> (min: Int, max: Int)? {
		if array.isEmpty {return nil}
		var curMin = array[0]
		var curMax = array[0]
		for value in array[1..<array.count] {
				if value < curMin {
						curMin = value
				}
				if value > curMax {
						curMax = value
				}
		}
		return (curMin, curMax)
}

// Use optional binding to check 
if let bound = minMax(array: [3, 5, 5, 6, 22, 13, 16, 24, 11]) {
		print("Min: \(bound.min)")
		print("Max: \(bound.max)")
}
```

With Implicit Return

- It only allow the first one sentence to implicit return.

```swift
// It will return the result of a + b
func sum(a: Int, b: Int) -> Int{
		a + b
}

// It will not return a value
func sum(a: Int, b: Int) {
		a + b
}

// This can't work!!! It will trige compile-time error.
func getTwoNum() -> Int {
    var a = 2
    var b = 4
    a + b
		// Here not allow to implicit return value
		// The implicit return only allow the first one sentence
}

// This can work, because the getTwoNum only use first one sentence to return
func get() -> (Int, Int) {
    var a = 5
    return (5, 6)
}
func getTwoNum() -> (Int, Int) {
    get()    // The implicit return can work.
}
```

## Parameter

### Parameter Names and Argument Labels

```swift
// Argument Labels
func greet(person: String, from hometown: String) -> String {
		// The 'person' and 'hometown' are parameter names.
		// The 'from' is a argument label of th second parameter.
		return "\(person) is come from \(hometown)."
}
print(greet(person: "Mike", from: "New York"))

```

### Omitting Argument Labels

Use the underscore to omit the label

```swift
func readFromFile(_ path: String) {
		print(path)
}

readFromFile("./music/one.mp3")
```

### Default Parameter Values

```swift
func drawRect(x: Int, y: Int, w: Int, h: Int, color: (Int, Int, Int) = (0, 0, 0)) {
		// Here the color has default value (0, 0, 0)
}
```

### Variadic Parameter

- The values passed will be made as as an **array.** For example, the `Double...` passed will be made as `[Double]`

```swift
func average(_ nums: Double...) -> Double{
		var total: Double = 0
		for num in nums {
				total += num
		}
		return total / Double(nums.count)
}
print(average(3.3, 2.9, 4.6))
```

- To make it unambiguous between the variadic parameter and normal parameters. The first parameter that comes after a variadic parameter must have an apparent argument label.
    
    ```swift
    func getOne(_ nums: Double..., cut cutLen: Int) -> Double{
    		
    		return 1.0
    }
    print(getOne(3.3, 2.9, 4.6, cut: 3))
    
    // Another way
    func getAnotherOne(_ nums: Double..., cutLen: Int) -> Double{
    		return 1.0
    }
    print(getAnotherOne(3.3, 2.9, 4.6, cutLen: 3))
    ```
    

### In-Out  Parameters

Function parameters are constants by default.

To modify the parameter’s value and let the changes persist after the function call, we can use the  `inout` keyword.

(Using the `inout` keyword, we can **change the origin value** via the function call)

The `inout` value can’t have a default value, variadic parameters can’t be marked as `inout` . 

- Without `inout`
    
    ```swift
    
    func swap(_ a: Int, _ b: Int) {
    		var temp = a
    		a = b       // It will trigger the compile-time error. 
    		b = temp    // The parameters are constant.
    }
    
    var x = 2
    var y = 3
    swap(x, y)
    print("x = \(x), y = \(y)"
    ```
    
- With `inout`
    
    ```swift
    func swap(_ a: inout Int, _ b: inout Int) {
    		var temp = a
    		a = b
    		b = temp
    }
    
    var x = 2
    var y = 3
    swap(&x, &y)   // Here should use the reference sign so that the funtion can konw where to change the value of the original variable.
    print("x = \(x), y = \(y)")
    // It will print 「x = 3, y = 2」
    ```
    

### Function Types

To describe a function’s input and output.

Such as `(Int, Int) -> Int` or `() -> Void` .

```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}

var mathFunction: (Int, Int) -> Int = addTwoInts
```

Function Types as Parameter Type

```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}

func printResult(_ mathFunc: (Int, Int) -> Int, _ a: Int, _ b: Int) {
		print("Result: \(mathFunc(a, b))")
}

printResult(addTwoInts, 2, 7)
// Print 「Result: 9」
```

Function Types as Return Types

```swift
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}

// Return a function type
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
		return backward ? stepBackward : stepForward
}

var cur = 3
let moveNearToZero = chooseStepFunction(backward: cur > 0)
// So the moveNearToZero will refers to the stepBackward() function.

while cur != 0 {
		print("Cur: \(cur)")
		cur = moveNearToZero(cur)
}

```

- result:
    
    cur: 3
    cur: 2
    cur: 1
    

## Nested Function

Hidden from the outside world by default. 

**Only when the nested function be returned, it can be used in the caller’s scope.**

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}

var cur = -4
let moveNearToZero = chooseStepFunction(backward: cur > 0)
// moveNearToZero now is refers tyo the nested stepForward()
cur = moveNearToZero(cur)
```