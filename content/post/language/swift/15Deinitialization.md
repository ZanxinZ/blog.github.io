---
title: "Deinitialization"
date: 2022-05-02T16:00:40+08:00
ShowToc: true
summary: Before the object is recycle, the deinitializer solve the post events.
tags:
- 编程语言
categories:
- Swift
---


A deinitializer is called immediately before a class instance is deallocated.

Write with `deinit` keyword.

Swift manage the instance memory through automatic reference counting.

Deinitializer can access all properties of the instance, and do some work to release the handlers (such as close a file).

Class definition can have at most one deinitializer per class. It doesn’t take any parameters.

```swift
// The function creat a cat and the cat will destory after the function end.
class Cat {
    init () {
        print("Create")
    }
    deinit {
        print("Destory")
    }
}
func run() {
    var cat = Cat()
}

run()

// Print:
// Create
// Destory
```

## Deinitializers in Action

Here shows a static Bank that own some coins. When create a new player, the player get coins from the Bank. And the player give back the coins to the Bank before the player is deinitialized.

```swift
class Bank {
    static var coins = 10000
    static func distribute(coinsRequest: Int) -> Int {
        let num = min(coinsRequest, coins) // It can only get at most 'coins' count.
        coins -= num
        return num
    }
    static func recive(num: Int) {
        coins += num
    }
}

class Player {
    var coins: Int
    init(coins: Int) {
        self.coins = coins
        Bank.distribute(coinsRequest: coins)
    }
    func win(coins: Int) {
        self.coins += Bank.distribute(coinsRequest: coins)
    }
    deinit {
        Bank.recive(num: coins)
    }
}
func create() {
    var mike = Player(coins: 300)
    print("After init \(Bank.coins)")
    mike.win(coins: 200)
    print("After win \(Bank.coins)")
}

create()
print("After deinit \(Bank.coins)")

// Print: 
// After init 9700
// 9500
// 10000
```