---
title: "Memory Safty"
date: 2022-06-12T16:05:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---


Most of the time we don’t have to think about accessing memory, but it’s important to understand where potential conflicts can occur, so we can avoid writing code that has conflicting access to memory.

Here we are talking about the situation that happen on a single thread.

## Memory Access

```swift
var one = 1     // write access to the memory one is stored.
print("\(one)") // read access from the memory one is stored.
```

The conflicting access to memory can occur when different part of the code are trying to access the same location in memory at the same time.

### Characteristics of Memory Access

A conflict occurs if two access that meet all of the following conditions:

- At least one is a write access or a nonatomic access.
- They access the same location in memory.
- Their duration overlap.

The different between a read and write access is obvious: a write access changes the location in memory, but a read access doesn’t.

An operation is *atomic* if it use only `C` atomic operation; otherwise it’s nonatomic.

Most memory access is instantaneous.

Example: all the read and write accesses in the code below are instantaneous

```swift
func addOne(_ number: Int) -> Int {
    return  number + 1
}
var num = 2
num = addOne(num)
print(num)
```

The different way to access memory:

- instantaneous access: as the code shown below.
- long-term access: other code can run after a long-term access but before it ends, which is call *overlap.* A long-term access can overlap with other long-term accesses and instantaneous access.

The situation that overlap appear:

- in-out parameters in functions.
- mutating methods of a structure.

## Conflicting Access to In-Out Parameters

A function has long-term write access to all of its in-out parameters. The write access for an in-out parameter starts after all of the non-in-out parameters have been evaluate and lasts for the entire duration of that function call. The in-out write access start in the same order as the parameter appear.

### Long-term write access to in-out parameter

- One consequence
    
    For long-term write access, we can’t access the original variable that was passed as in-out, even if scoping rules and access control permit it.
    
    ```swift
    var step = 1
    func increment(_ number: inout Int) {
    		// It want to read step and write number
        number += step  // Here has a read and write overlap, the number and step refer to the same location in memory. 
    }
    
    increment(&step) // Error conflicting access to "step"
    ```
    
    One way to solve the conflict above is to make a copy of step.
    
    ```swift
    var copyOfStep = step // Copy from original.
    increment(&copyOfStep)// Call function with the copy.
    step = copyOfStep     // Update the original.
    ```
    
- Another consequence
    
    Passing a single variable as the argument for multiple in-out parameter of the same function produces a conflict.
    
    ```swift
    func balance(_ x: inout Int, _ y: inout Int) {
        let sum = x + y
        x = sum / 2
        y = sum / 2
    }
    var a = 20
    var b = 10
    
    balance(&a, &b)  // It's Ok, there are two write access overlap in time but access different location.
    balance(&a, &a)  // It's conflict, it pass "a" to the two in-out parameter, there will be two write access overlap in time and memory.
    ```
    

## Conflicting Access to self in Methods

A mutating method on a structure has write access to `self` for the duration of the method call.

```swift
func balance(_ x: inout Int, _ y: inout Int) {
    var sum = x + y
    x = sum / 2
    y = sum / 2
}

struct Player {
    var name: String
    var health: Int
    var energy: Int
    static let maxHealth = 100
    mutating func restorehealth() {
        health = Player.maxHealth // Here access the "self.health"
    }
    
    mutating func shareHealth(with teammate: inout Player) {
        balance(&teammate.health, &health)
    }
}

var oscar = Player(name: "Oscar", health: 100, energy: 90)
var maria = Player(name: "Maria", health: 60, energy: 90)
oscar.shareHealth(with: &maria) // It's Ok, different write access to different memory.

oscar.shareHealth(with: &oscar) // Error: conflicting accesses to the same memory (the memory that oscar's health refer to).
```

## Conflicting Access to Properties

Types like structures, tuples and enumerations are value type, mutating any piece of the value will mutate the whole value, meaning read or write access to one of the properties requires read or write access to the whole value.

```swift
var holly = Player(name: "Holly", health: 10, energy: 10)
balance(&holly.health, &holly.energy)    
// Error, "holly" refers to an entire value type, health an energy are two peice of that value, 
// the two in-out of the function can't write accees to the overlap memory.
```

However, if the `holly` in the above example is changed to a local variable instead of a global variable, it can be safe.

```swift
func someFunc() {
    var holly = Player(name: "Holly", health: 10, energy: 10)
    balance(&holly.health, &holly.energy)
		// The compiler can prove that memory access is safety because the two stored properties don't interact in any way.
}

someFunc()  // It's OK.
```

The overlapping access to properties of a structure isn’t always necessary to preserve memory safety. **Exclusive access** is a stricter requirement than memory safety.

It can prove that overlapping access to properties of a structure is **safe** if the following condition apply:

- Accessing only stored properties of an instance, not computed or class properties.
- The structure is a local variable, not a global variable.
- The structure is not capture by any closures, or its captured only by non-escaping closures.

If the compiler can’t prove the access is safe, it doesn’t allow the access.