---
title: "Method"
date: 2022-04-17T16:15:40+08:00
summary: The object have some method to provide functionality.
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---
# Method

Methods are functions that associated with a particular type.

Classes, structures and enumerations.

- Define instance methods for an instance of the given type.
- Define type method for the type itself.

## Instance Method

It is defined in a type. And it can only be called by a specific instance of the type.

```swift
class Bird {
    var energy = 10
    
		// Instance method
    func sing() {
        energy -= 1
    }
    
		// Instance method
    func eatFood(food: Int) {
        self.energy += food   // self is current instance of the type, it's the instance who call the current method.
    }
		
		func setEnergy(energy: Int) {
        self.energy = energy // Here must use the 'self' to distinguish the property of the instance and the method's parameter.
    }
}

var keo = Bird()
keo.eatFood(food: 4)
print(keo.energy)   // 14
keo.sing()
print(keo.energy)   // 13
```

### Modifying Value Types in the Instance Method

Structures and Enumerations are value type, their properties can’t be modified by default.

However, we can use the `mutating` keyword to enable the method to modify their properties.

```swift
struct Point {
    var x = 0
    var y = 0
    
    mutating func moveTo(x: Int, y: Int) {
        self.x = x
        self.y = y
    }
}

// Assigning to self Within a Mutating Method, it has the same result as the code above.

struct Point {
    var x = 0
    var y = 0
    
    mutating func moveTo(x: Int, y: Int) {
        self = Point(x: x, y: y)
    }
}

enum Level {
    case off, low, high
    mutating func next() {
        switch self {
        case .off:
            self = .low
        case .low:
            self = .high
        case .high:
            self = .off
        }
    }
}
```

## Type Method

- Defined in the type, called by the type.
- Can be used in classes, structures, enumerations.

Use the `static` keyword to indicate the function is the type method.

```swift
struct Boy {
    static var count = 0
    static func add() {
        count += 1
    }
}

var boy = Boy()
Boy.add()
var boyB = Boy()
Boy.add()
print(Boy.count)  // 2
```

Classes can use the `class` key word instead of the `static` to allow the subclass to override the method. (Just like a `protected` keyword in Java)

```swift
class Shape {
    static var count = 0
    class func add() {
       self.count += 1   // Here the 'self' is the type Shape itself, not a instance of type.
    }
}
```

### Example:

The player class create a new instance of `LevelTracker` to track that player’s progress.

```swift
struct LevelTracker {
    static var highestUnlockedLevel = 0
    var currentLevel = 0
    
    static func unlock(_ level: Int) {
        if level > highestUnlockedLevel {
            highestUnlockedLevel = level
        }
    }
    
    static func isUnlocked(_ level: Int) -> Bool {
        return level <= highestUnlockedLevel
    }
    
    @discardableResult
    mutating func advance(to level: Int) -> Bool {
        if LevelTracker.isUnlocked(level) {
            currentLevel = level
            return true
        } else {
            return false
        }
    }
}

class Player {
    var tracker = LevelTracker()
    let playerName: String
    func complete(level: Int) {
        LevelTracker.unlock(level + 1)  // Unlock the next level for all players.
        tracker.advance(to: level + 1)  // Update current player's progress to next level.
    }
    
    init(name: String) {
        playerName = name
    }
}

var player = Player(name: "Mike")

player.complete(level: 0)
print(player.tracker.currentLevel)      // 1
print(LevelTracker.highestUnlockLecel)  // 1
```