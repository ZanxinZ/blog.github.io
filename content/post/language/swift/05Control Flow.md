---
title: "Control Flow"
date: 2022-03-28T16:08:40+08:00
summary: loop, condition branch, control transfer.
ShowToc: true
tags: 
- 编程语言
categories: 
- Swift
---

- loop
    - while
    - for-in
    - repeat while
- condition branch
    - if
    - switch
        
        where
        
    - guard
- control transfer statements
    - break
    - continue
    - fallthrough
    - return
    - throw

---

## Loop

### For-In Loops

```swift
var pets = ["cat", "dog"]
for pet in pets {
    print(pet)
}
for index in 0..<2 {
		print(pets[index])
}

// if we don't need the value from the 0..<2, use the underscore to ignore the value.
var n = 1
for _ in 0..<2 {
		print(n)
		n += 1
}

var words = [11: "fine", 2: "happiness"]
for (key, word) in words {
		print("\(key): \(word)")
}
```

Stride Function

```swift
let minutes = 60
let interval = 5
for tickMark in stride(from: 0, to: minutes, by: interval) {
		// render the mark every 5 minutes. 
		// 0, 5, 10, ... , 50, 55
}

// closed range
for tickMark in stride(from: 0, through: minutes, by: inerval {
		// render the mark every 5 minutes.
		// 0, 5, 10, ... , 50, 55, 60
}
```

The for-in can be only use to the object which conform to the **Sequence protocol**.

### While

```swift
var a = 0
while a < 10 {
		print(a)  // 0 to 9
		a += 1
}
```

### Repeat While

The content that repeat while enclose with `{}` will run at least once.

```swift
var a = 0
while a < 0 {
		print(a)
		a += 1
}
// will not print

repeat {
		print(a)
		a += 1
}while a < 0
// will print 0
```

---

## Condition

### If

```swift
var a = 4
if a < 5 {
		// smaller than 5
} else if a > 5 {
		// bigger than 5
} else {
		// equal to 5
}
```

### Switch

The body of each case must contain at least one executable statement.

**No Implicit Fallthrough: no need to write ‘break’ in each case, switch do only one case.**

```swift
var s: String = "as"
switch s {
		case "aa", "AA":
				print("not the target")
		case "as":
				print("yes")
		default:
				print("I don't know")
}
```

Interval Matching

```swift
var a = 40
var amount: String?
switch a {
		case ..<0:
				amount = nil 
		case 0: 
				amount = "no"
		case 1...10:
				amount = "few"
		case 11..<50:
				amount = "some"
		case 50..<100:
				amount = "many"
		default:
				amount = "much"
}
```

Tuples

- Use underscore as a wildcard pattern.
- The point (0, 0) would match `case (0, 0)` first, and so all other matching cases would be ignored.

```swift
let point = (1, 1)
switch point {
case (0, 0):
    print("It's origin")
case (_, 0):
    print("It's on x axis")
case (0, _):
    print("It's on y axis")
case (-2...2, -2...2):
    print("It's in the box")
default:
    print("Out of box")
}
```

Value Bindings

- The case can name value or values from the matched test, for use in the body of the case.
- In this way, we can choose a matched pattern to do specific work.

```swift
let point = (2, 0)
switch point {
case (let x, 0):
    print("(\(x),0) is on x axis")
case (0, let y):
    print("(0, \(y) is on y axis")
case let(x, y):
    print("(\(x), \(y)) not on axis")
}
```

Where

- Add a condition for the value or values from the test.

```swift
let point = (2, -2)
switch point {
case let(x, y) where x == y:
    print("(\(x), \(y)) is on the line y = x")
case let(x, y) where x == -y:
    print("(\(x), \(y) is on the line y = -x")
case let(x, y):
    print("(\(x), \(y)) others")
}
```

Compound Cases

- Multiple cases can compounded into one.
- The pattern can be written over multiple lines.

```swift
let c = "a"
switch c {
case "a", "e", "i", "o", "u":
    print("It's a vowel.")
case "b", "c", "d", "f", "g",
"h", "j", "k", "l", "m", "n",
"p", "q", "r", "s", "t", "v",
"w", "x", "y", "z":
    print("It's a consonant.")
default:
    print("It's not an alphabet.")
}
```

- Compound with tuple binding
    
    Every pattern in a case must include the temporary object(`distance`), so the code in the body of the `case` can always access a value for `distance`.
    
    ```swift
    let point = (1, 0)
    switch point {
    case (0, let distance), (let distance, 0):
        print("point is on the axis, distance \(distance)")
    default:
        print("point isn't on the axis")
    }
    ```
    
    ---
    
    ## Control Transfer Statements
    
    ### Continue
    
    Tells a loop to stop what it’s doing and start again at the beginning of the next iteration through the loop.
    
    ```swift
    var nums: [Int] = []
    for n in  1...10 {
        if n % 2 == 0 {
            continue
        }
        nums.append(n)
    }
    print(nums)
    ```
    
    - resule
        
        [1, 3, 5, 7, 9]
        
    
    ### Break
    
    ```swift
    var nums: [Int] = []
    for n in  1...10 {
        if n == 5 {
            break
        }
        nums.append(n)
    }
    print(nums)
    ```
    
    - result
        
        [1, 2, 3, 4]
        
    
    Break in a switch
    
    - comments aren’t statement, use a `break` when the case body is empty.
    
    ```swift
    let n = 5
    switch n {
    case 1:
        print("one")
    case 2:
        print("I want to break")
        break
    		print("two")   // it won't do this
    case 3:
        print("three")
    default:
        break
    }
    ```
    

### Fallthrough

In Swift , one`switch` only execute one case . If we want to execute multiple cases, use the `fallthrough` .

```swift
let n = 5
switch n {
case 5:
    print("five")
    fallthrough    // go on the code in the switch
default:
    print("believe it or not")
    break
}
```

### Labeled Statements

The `break` and `continue` can only affect the current `for`, `while`, `switch`, `repeat while`. So when we want to break or continue the outside loop or switch, we can use the labeled statements.

- break
    
    ```swift
    var a = 1
    var sum = 0
    aLoop: while a < 10 {
        while a < 5 {
            if a == 3 {
    						a += 5
                break aLoop
            }
            a += 1
            print("add a: \(a)")
        }
        print(a)
    		a += 1
    }
    ```
    
    - result
        
        add a: 2
        add a: 3
        
- continue
    
    ```swift
    var a = 1
    var sum = 0
    aLoop: while a < 10 {
        while a < 5 {
            if a == 3 {
                a+=5   // now the a is 8
                continue aLoop
            }
            a += 1
            print("add a: \(a)")
        }
        print(a)
        a += 1
    }
    ```
    
    - result
        
        add a: 2
        add a: 3
        8
        9
        
        It continue the outside while loop.
        
    
    ---
    
    ## Early Exit
    
    A `guard` must have else clause after it.
    It’s to exit early.
    
    ```swift
    func play(ball: String?) {
        guard let str = ball else {
            print("no ball")
            return   // the guard's else can't fall through, here we use the return to end the excution.
        }
    		print(str)   // it's available after the guard's statement.
    }
    
    play(ball: nil)
    ```
    
    ## Checking API Availability
    
    ```swift
    if #available(iOS 10, macOS 10.12, *) {
    		
    } else {
    		// Fall back to earlier iOS and macOS APIs
    }
    ```