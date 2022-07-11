---
title: "Collection Type"
date: 2022-03-28T16:04:40+08:00
summary: The array, set, and dictionary.
ShowToc: true
tags: 
- 编程语言
categories: 
- Swift
---

- array
- set
- dictionary

collection type use are generic, the type of element is specific.

---

## Array

- With ordering.
- Access by subscript.
- Variable-length.

```swift
var someInts: [Int] = []
someInts.append(2)
someInts = []      // Empty the array
```

Create by class

```swift
var someDoubles = Array(repeating: 0.0, count: 3)
print(someDoubles)
var otherDoubles = Array(repeating: 2.5, count: 3)
print(otherDoubles)
var addedDoubles = someDoubles + otherDoubles    // concatenate two array into one
print(addedDoubles)
```

- result:
    
    [0.0, 0.0, 0.0]
    [2.5, 2.5, 2.5]
    [0.0, 0.0, 0.0, 2.5, 2.5, 2.5]
    

Create By Array Literal

```swift
var shoppingList = ["tomato", "milk"]
```

```swift
// Appending
shoppingList.append("noodles")
shoppingList += ["rice"]
shoppingList += ["apple", "juice"]

// Now the array is ["tomato", "milk", "noodles", "rice", "apples", "juice"]
```

Accessing 

```swift
print("Count of stuff: \(shoppingList.count)")  // the 'count' property is read-only.
```

```swift
if shoppingList.isEmpty {
		print("Nothing to buy.")
} else {
		print("Have something to buy.")
}
```

```swift
// Accessing
var oneStuff = shoppingList[2]  // get the "noodles"
```

Modifying

```swift
// Modifying
shoppingList[2] = "dumplings"
// Modifying a range of value
shoppingList[3...5] = ["pie", "cherry", "banana"] 
shoppingList[3...5] = ["pie", "cherry"]                      // it will remove the element from 3 to 5 and just insert with two new element at position 3.
//shoppingList[3...5] = ["pie", "cherry", "bread", "cookie"] the 5 is out of array's bound now, so it will trigger a run-time error.
shoppingList[3...4] = ["pie", "cherry", "peach"]             // it can work, just replace two element shoppingList[3] and shoppingList[4] with three new elenment. 
// now the array is ["tomato", "milk", "dumpings", "pie", "cherry", "peach"]

// insert(_:at:)
shoppingList.insert("drink", at: 0)
// now the shoppingList is: ["drink", "tomato", "milk", "dumpings", "pie", "cherry", "peach"]

// remove(at:)
let thing = shoppingList.remove(at: 1)
// The "thing" is assiged to be "tomato"
// Any gaps in an array are closed when an item is removed.
// So after the removing, the shoppingList is: ["drink", "milk", "dumpings", "pie", "cherry", "peach"]

let last = shoppingList.removeLast() // remove the "peach"

var str = shoppingList[0]
str.append("s")             // this will not affect the string in the shoppingList, because the String is value type, it will be copied when be passed (assigned).

shoppingList[0].append("s")// this will affect the string in the shoppingList.

// now the array is ["drinks", "milk", "dumpings", "pie", "cherry"]
```

Iterating Over an Array

```swift
for item in shoppingList {
		print(item)
}
```

```swift
for (i, value) in shoppingList.enumerated() {
		print("Item \(i): \(value)"
}
// here the 'i' is a subscript of the array.
```

---

## Sets

- With no defined ordering.
- An item only appears once.

**A type must be *hashable* in order to be stored in a set**—that is, the type must provide a way to compute a *hash value* for itself. if two objects’ hash values are equal, they are equally.

All of Swift’s basic types are hashable by default. (`String`, `Int`, `Double`,  `Bool`, `Enumeration`)

### Create a set

- create by an `Array` literal
    
    ```swift
    var words: Set<Character> = ["d", "i", "v", "e"]
    print(words)
    print(words.count)
    ```
    
    - result
        
        ["d", "i", "e", "v"]
        4
        
    
    In a shorter form
    
    ```swift
    var words: Set = ["h", "i", "d", "e"]
    ```
    
- create an instance of class `Set`
    
    ```swift
    var letter = Set<Character>()
    letter.insert("H")
    letter.insert("e")
    letter.insert("a")
    letter.insert("r")
    print(letter)
    print(letter.count)
    ```
    
    - result
        
        ["a", "r", "e", "H"]
        4
        

### Accessing

```swift
var words: Set = ["hide", "ice", "pink", "big"]
print(words.count)   // it's read-only
if words.isEmpty {
		print("words is empty.")
}
```

### Modifying

```swift
words.insert("book")

var wordGet = words.remove("ice")  // if words doesn't contain "ice", the removing will be invalid and the wordGet will be nil.
```

### Iterating Over a Set

```swift
for word in words {
		print(word)
}
```

Iterating  in a specific order

```swift
for word in words.sorted() {
		print(word)
}
```

### Set Operations

| intersection(_: ) | create a new set with only the values common to both sets |
| --- | --- |
| symmetricDiffierence(_: ) | create a new set with values in either set, but not both |
| union(_: ) | create a new sew with values with all of the values in both sets |
| subtracting(_: ) | create a new set with values not in the ‘b’ set |

```swift
var a: Set = [1, 2, 5, 6]
var b: Set<Int> = [1, 3, 5]

var intersection = a.intersection(b)      // a & b
var difference = a.symmetricDifference(b) // !a & !b
var union = a.union(b)                    // a or b
var subtract = a.subtracting(b)           // a - b

print(intersection)
print(difference)
print(union)
print(subtract)
```

- result:
    
    [5, 1]
    [2, 6, 3]
    [3, 2, 1, 6, 5]
    [6, 2]
    

### Membership and Equality

- `==`
- `isSubset(of: )`
- `isSuperset(of: )`
- `isStrictSubset(of: )`
    
    It’s a subset but not equal to.
    
- `isStrictSuperset(of: )`
    
    It’s a superset but not equal to.
    
- `isDisjoint(with: )`
    
    Two set has no values in common. They are independent with each other.
    
    ```swift
    let all: Set = ["😂", "😎", "😺", "🙋🏼‍♂️"]
    let me: Set = ["🙋🏼‍♂️"]
    let emoj: Set = ["😂", "😎"]
    let other: Set = ["😂", "😎", "😺", "🙋🏼‍♂️"]
    print("'me' is subset of 'all': " + String(me.isSubset(of: all)))
    print("'all' is superset of 'emoj': " + String(all.isSuperset(of: emoj)))
    
    print("'all' is superset of 'other': " + String(all.isSuperset(of: other)))
    print("'all' is subset of 'other': " + String(all.isSubset(of: other)))
    print("'all' is strict superset of 'other': " + String(all.isStrictSuperset(of: other)))
    print("'all' is strict subset of 'other': " + String(all.isStrictSuperset(of: other)))
    
    print("'other' is superset of 'all': " + String(other.isSuperset(of: all)))
    print("'other' is subset of 'all': " + String(other.isSubset(of: all)))
    ```
    
    - result:
        
        'me' is subset of 'all': true
        'all' is superset of 'emoj': true
        'all' is superset of 'other': true
        'all' is subset of 'other': true
        'all' is strict superset of 'other': false
        'all' is strict subset of 'other': false
        'other' is superset of 'all': true
        'other' is subset of 'all': true
        

---

## Dictionary

- keys have same type, values have same type
- with no ordering
- each value is associated with a **unique key**
- key is unique, values may be same

> Swift’s `Dictionary` type is bridged to Foundation’s `NSDictionary`class.
The key must conform to the Hashable protocol.
> 

Create a dictionary

- by class
    
    ```swift
    var pet =  Dictionary<Int, String>()
    pet[11] = "dog"    // adding key-value pair
    pet[23] = "bear"
    ```
    
- by literal
    
    ```swift
    var pet = [11: "dog", 23: bear]
    ```
    
- properties

```swift
print(pet.count)
print(pet.isEmpty)

if pet[23] != nil {
    print(pet)
}
```

- result
    
    2
    false
    

Updating

```swift
pet[11] = "cat"   // update the value

if let oldValue = pet.updateValue("deer", forKey: 23) {
    // now the pet is: [11: "cat", 23: "deer"], and the oldValue is bear(optional).
} else {
    print("oldValue is nil")
}
```

Removing

```swift

pet[11] = nil               // remove a key-value pair.

pet.removeValue(forKey: 23) // remove a key-value pair.

pet = [11: "cat", 23: "dog"]
pet.removeAll() 
pet = [11: "cat", 23: "dog"]
pet = [:]                   // clear all
```

Iterating Over a Dictionary

- by key and value
    
    ```swift
    for (key, value) in pet {
        print("\(key): \(value)", terminator: "\t")
    }
    ```
    
    - result
        
        23: dog    11: cat
        
- by key or value
    
    ```swift
    var pet = [11: "cat", 23: "dog"]
    print()
    for key in pet.keys {
        print("\(key) ", terminator: " ")
    }
    print()
    
    for value in pet.values {
        print("\(value)", terminator: " ")
    }
    ```
    
    - result
        
        
        11  23
        cat dog
        

Get an Array from key or value

- 
    
    ```swift
    let keys = [Int](pet.keys)
    ```
    
- 
    
    ```swift
     let values = [String](pet.values)
    ```
    
- get in order by calling `sorted()`, the object who call the sorted should implements the Comparable.
    
    ```swift
    let keys = [Int](pet.keys.sorted())
    ```
    
- the `pet.sorted(using: )` need a Comparator to sort the dictionary.

---

## Summary

- Creating
    - Array
        
        `var pet: [String] = ["dog", "cat"]`
        
    - Set
        
        `var pet: Set<String> = ["dog", "cat"]`
        
        `var pet: Set = ["dog", "cat"]`
        
    - Dictionary
        
        `var pet: Dictionary<Int, String> = Dictionary<Int, String>()`
        
        `var pet = [:]`
        
        `var pet: Dictionary<Int, String> = [23: "dog", 11: "cat"]`
        
        `var pet = [23: "dog", 11: "cat"]`
        
- Accessing and Modifying
    
    the `Array` and `Dictionary` can access by `[someKey]`directly , but the `Set` access by `insert()` and `remove()` .
    
- For-in
    - Array
        
        ```swift
        for word in words {}
        ```
        
    - Set
        
        ```swift
        for word in words {}
        ```
        
    - Dictionary
        
        ```swift
        for (key, value) in words {}
        ```
        
- Operation
    
    To generate new set, `Set` has some operations between two sets.