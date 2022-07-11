---
title: "Protocol"
date: 2022-05-22T16:00:40+08:00
ShowToc: true
summary: A protocol defines a blueprint with methods, properties.
tags:
- 编程语言
categories:
- Swift
---

A *protocol* defines a blueprint of methods, properties. 

Can be Adopted and implemented by: 

- class
- structure
- enumeration

## Syntax

Definition

```swift
protocol SomeProtocol {
		func doSomething()
}
```

Being Conformed

- By Struct
    
    ```swift
    struct SomeStruct: FirstProtocol, AnotherProtocol {
    
    }
    ```
    
- By Class
    
    ```swift
    class SomeClass: SomeSuperClass, FirstProtocol, AnotherProtocol {
    
    }
    ```
    

## Conforming Protocol

The `Dog` and `People` both conform the `DoSport` protocol, so they have responsibility to implement all things from the protocol.

```swift
protocol DoSport {
    var strength: Int { get set}
    static var instanceCount: Int { get}
    func walk(distance: Int)
}

class Dog: DoSport {
    static var instanceCount: Int = 0

    func walk(distance: Int) {
        print("Walk \(distance) with four leg")
    }
    var strength: Int
    init(strength: Int) {
        self.strength = strength
        Dog.instanceCount += 1
    }
    
}

class People: DoSport {
    static var instanceCount = 0
    func walk(distance: Int) {
        print("Walk \(distance) with two leg")
    }
    var strength: Int
    init(strength: Int) {
        self.strength = strength
        People.instanceCount += 1
    }
}

var d = Dog(strength: 200)
d.walk(distance: 4000)
var p = People(strength: 1000)
p.walk(distance: 1000)
```

### Mutating Method Requirements

```swift
protocol Togglable {
    mutating func toggle()
}
```

The `mutating` keyword only used by structures and enumerations.

### Initializer Requirements

```swift
protocol SomeProtocol {
    init(someParameter: Int)
}
```

Class Implementation

The `required` modifier ensures that all the subclass of the `someClass` must conform to the protocol and implement the method.

```swift
class SomeClass: SomeProtocol {
    required init(someParameter: Int) {
        
    }
}
```

Subclass Override Initializer and Conform to the Protocol

```swift
protocol Cuttable {
    init(someParameter: Int)
}

class Shape {
    init(someParameter: Int) {
        
    }
}
class Square: Shape, Cuttable {
    required override init(someParameter: Int) {
        super.init(someParameter: someParameter)
    }
}
```

### Failable Initializer Requirements

A failable initializer requirement can be satisfied by a failable or nonfailable initializer on a conforming type. A nonfailable initializer requirement can be satisfied by a nonfailable initializer or an implicitly unwrapped failable initializer.

## Protocol as Types

Using a protocol as type is sometimes called an existential type. So call “A type *T* conform to the protocol”.

Use a protocol as a type:

- As a parameter type or return type in a function, method, or initializer
- As the type of a constant, variable, or property
- As the type of items in an array, dictionary, or other container

Example: 

Use the `RandomNumberGenerator` protocol as a type, the parameter that the initializer get should conform to the `RandomNumberGenerator` .

```swift
protocol  RandomNumberGenerator {
    func random() -> Double
}

class Dice {
    let sides: Int
    let generator: RandomNumberGenerator
    init(sides: Int, generator: RandomNumberGenerator) {
        self.sides = sides
        self.generator = generator
    }
    func roll() -> Int {
        return Int(generator.random() * Double(sides)) + 1
    }
}

class CustomGenerator: RandomNumberGenerator {
    func random() -> Double {
        return 3.0
    }
}

// When the CustomGenerator() instance pass to the initialzer, it's been upcasting.
var dice = Dice(sides: 2, generator: CustomGenerator()) // Give it a generator. It's a way to make generics.
print(dice.roll()) // 7
```

## Delegation

A class has a delegation in itself and the delegation take the place of class to do other things. **The delegation’s input is the class whom want to be delegated.**

### Example: A snake and ladder game.

The `DiceGame` is a protocol that require a `Dice` and a `play()` method. It’s the fundamental function of a `DiceGame` .

The `DiceGameDelegate` is a protocol that required some method to do the detail of the game, it defines how the game be played. **It rely on the** `DiceGame` . So here is the *Delegation.*

The `SnakeAndLadders` implements the `DiceGame` , and implements what to do in the `play()` method. It has a `DiceGameDelegate` to do delegate itself to do the detail things.

```swift
class Generator {
    func getNum() -> Int { Int.random(in: 1...6)}
}

class Dice {
    let sides: Int
    var generator: Generator
    init(sides: Int, generator: Generator) {
        self.sides = sides
        self.generator = generator
    }
    func roll() -> Int {
        return generator.getNum()
    }
}
protocol DiceGame {
    var dice: Dice{get}
    func play()
}

protocol DiceGameDelegate: AnyObject {
    func gameDidStart(_ game: DiceGame)
    func game(_ game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int)
    func gameDidEnd(_ game: DiceGame)
}

class SnakeAndLadders: DiceGame {
    let finalSquare = 25
    let dice = Dice(sides: 6, generator: Generator())
    var square = 0
    var board: [Int]
    init() {
        board = Array(repeating: 0, count: finalSquare + 1)
				// Some point in the board have extra square buffer.
        board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
        board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    }
    
    weak var delegate: DiceGameDelegate?
    func play() {
        square = 0
        delegate?.gameDidStart(self)
				
        gameLoop: while square != finalSquare {
            let diceRoll = dice.roll()
            delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll)
            switch square + diceRoll {
            case finalSquare:
                break gameLoop    // If the score(square + diceRoll) equal to max, the game end
            case let newSquare where newSquare > finalSquare:
                continue gameLoop // If the score(square + diceRoll) is greater than max, try again to get a diceRoll.
            default: 
                square += diceRoll // If the score(square + diceRoll) is smaller than max, the game loop go on.
                square += board[square]
            }
        }
        delegate?.gameDidEnd(self)
    }
    
}

class DiceGameTracker: DiceGameDelegate {
    var numberOfTurn = 0
    func gameDidStart(_ game: DiceGame) {
        numberOfTurn = 0
        if game is SnakeAndLadders {
            print("Start a new game of Snakes and Ladders")
        }
        print("Game use a \(game.dice.sides)")
    }
    
    func game(_ game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int) {
        numberOfTurn += 1
        print("Rolled a \(diceRoll)")
    }
    
    func gameDidEnd(_ game: DiceGame) {
        print("The game lasted for \(numberOfTurn) turns")
    }
}

// The use of game and the delegation.
let tracker = DiceGameTracker()  // It's a implementation of the DiceGameDelegate.
let game = SnakeAndLadders()     // It's a implementation of the DiceGame.
game.delegate = tracker
game.play()
```

## Adding Protocol Conformance with an Extension

We can extend an existing type to adopt and conform to a new protocol, even if we don’t have access to the source code for the existing type.

```swift
protocol TextOut {
    var textDesc: String { get }
}

extension Dice: TextOut {
    var textDesc: String {
        return "It's a dice with \(self.sides) sides"
    }
}

print(game.dice.textDesc)  // Print: It's a dice with 6 sides
```

### Conditionally Conforming to a Protocol

Condition: Where the element conform the TextOut

```swift
extension Array: TextOut where Element: TextOut {
    var textDesc: String {
        let itemAsText = self.map{$0.textDesc}  // It's a map
				// Join the map to be a string
        return "[" + itemAsText.joined(separator: ",") + "]"
    }
}

let d6 = Dice(sides: 12, generator: Generator())
let d12 = Dice(sides: 12, generator: Generator())
let myDice = [d6, d12]
print(myDice.textDesc) // [It's a dice with 12 sides,It's a dice with 12 sides]
```

### Declaring Protocol Adoption with an Extension

Use the instance as a protocol. So it’s the **protocol oriented programming.**

```swift
struct Hamster {
    var name: String
    var textDesc: String {
        return "A Hamster \(name)"
    }
}

extension Hamster: TextOut {}

let h = Hamster(name: "HH")
let p: TextOut = h
print(p.textDesc)
```

## Adopting a Protocol Using a Synthesized Implementation

Swift can ***automatically*** **provide the protocol conformance** for `Equatable` , `Hashable`, and `Comparable` in many simple cases.

Synthesized implementation of  `Equatable` 

- Structures that have only stored properties that conform to the `Equatable`protocol
- Enumerations that have only associated types that conform to the `Equatable`protocol
- Enumerations that have no associated types

Synthesized implementation of `Hashable` .

- Structures that have only stored properties that conform to the `Hashable`protocol
- Enumerations that have only associated types that conform to the `Hashable`protocol
- Enumerations that have no associated types

Example:

- Once the structure or enumeration conform the `Equatable` , it can be compared by `==`, and the structure of enumeration needn’t to implement that the protocol required.
    
    ```swift
    struct Vector3D: Equatable {
        var x = 0.0, y = 0.0, z = 0.0
    }
    let point = Vector3D(x: 2.0, y: 3.0, z: 4.0)
    let pointAnother = Vector3D(x: 2.0, y: 3.0, z: 4.0)
    
    if point == pointAnother {
        print("Two vectors are equivalent")
    }
    
    // Print: Two vectors are equivalent
    ```
    
- `Comparable`
    
    ```swift
    enum SkillLevel: Comparable {
        case beginner
        case intermediate
        case expert(stars: Int)
    }
    
    var levels = [SkillLevel.intermediate, SkillLevel.beginner, SkillLevel.expert(stars: 5), SkillLevel.expert(stars: 3)]
    
    for level in levels.sorted() {
        print(level)
    }
    
    // Print:
    // beginner
    // intermediate
    // expert(stars: 3)
    // expert(stars: 5)
    ```
    

## Collection of Protocol Types

A protocol can be used as the type to be stored in a collection such as an array or a dictionary.

```swift
let things: [TextOut] = [d6, d12, h]
for thing in things {
    print(thing.textDesc)
}

// Print:
// It's a dice with 6 sides
// It's a dice with 12 sides
// A Hamster HH
```

## Protocol Inheritance

A protocol can inherit one or more other protocols.

```swift
protocol Storeable {
    func store() -> String
}
protocol Readable {
    
}

protocol Cleanable {
    
}

protocol BookShelf: Storeable, Readable, Cleanable {
    // Here the BookShelf will have the requirement from the protocols.
}
```

## Class-Only Protocols

We can limit protocol adoption to *class types* by adding the `AnyObject` protocol to a protocol’s inheritance list. It defines that the conforming type has a reference semantics rather than value semantics.

```swift
protocol SomeClassOnlyProtocol: AnyObject, SomeInheritedProtocol {
}
```

## Protocol Composition

A type can conform to multiple protocols at the same time. It has the form `SomeProtocol & AnotherProtocol` .

Protocol composition don’t define any new protocol types.

```swift
protocol Named {
    var name: String { get }
}

protocol Aged {
    var age: Int { get}
}

struct Person: Named, Aged {
    var name: String
    var age: Int
}

// The type passed to the function must conform to both Named and Aged.
func goToSchool(menber: Named & Aged) {
    print("Go to school: \(menber.name) is \(menber.age)")
}

let person = Person(name: "Mike", age: 8)
goToSchool(menber: person)

// Print: Go to school: Mike is 8
```

### Combines the Protocol and Class

Combines the `Named` protocol and `Location` class.

```swift
protocol Named {
    var name: String { get }
}

func printInfo(location: Location & Named) {
    print("The place \(location.name)  [\(location.latitude), \(location.longitude)] ")
}

let place = City(name: "Teochew", latitude: 23.67, longitude: 116.62)
printInfo(location: place)

// Print: The place Teochew  [23.67, 116.62]
```

## Checking for Protocol Conformance

It can do the checking and casting as operation that in type checking and casting.

- `is` : check a protocol
- `as` : cast a protocol to a specific protocol.
    
    The `as?` is downcast operator, it return an optional value. The `as!` is force downcast, it can trigger runtime error.
    

```swift
// Protocol is used to describe some status.
protocol HasArea {
    var area: Double { get }
}
class Circle: HasArea {
    let pi = 3.1415927
    var radius: Double
    var area: Double { return pi * radius * radius}
    init(radius: Double) {
        self.radius = radius
    }
}

class Square: HasArea {
    var area: Double
    init(area: Double) {
        self.area = area
    }
}

class Animal {
    var legs: Int
    init(legs: Int) {
        self.legs = legs
    }
}

let objs: [Any] = [Circle(radius: 5), Square(area: 20), Animal(legs: 4)]

for obj in objs {
    if obj is HasArea {
        print(obj, " is HasArea")
    } else {
        print(obj, " is not HasArea")
    }
}

for obj in objs {
    if let object = obj as? HasArea {
				// Here the object is know to be of type HasArea, so it has the only property 'area' to access.
        print(object, " can be cast to a HasArea. Area is \(object.area)")
    } else {
        print(obj.self, " can't be cast to a HasArea")
    }
}

// Print:
// Page_Contents.Circle  is HasArea
// Page_Contents.Square  is HasArea
// Page_Contents.Animal  is not HasArea
// Page_Contents.Circle  can be cast to a HasArea Area is 78.5398175
// Page_Contents.Square  can be cast to a HasArea Area is 20.0
// Page_Contents.Animal  can't be cast to a HasArea
```

## Optional Protocol Requirements

Use the `@objc` to mark the protocol and the requirements. So these requirements don’t have to be implemented by types that conform to the protocol.

`@objc` :

- Can be adopted only by classes that inherit from Objective-C class or other `@objc` classes (Maybe in the new swift version, the class don’t have to inherit the OC class).
- Can’t be adopted by structures or enumerations.
- The type used in the optional requirement will automatically be an optional. Such as `(Int) -> String` becomes `((Int) -> String)?` . Here the entire function type is wrapped in the optional, not the method’s return value.

```swift
@objc protocol Source {
    @objc optional func increment(count: Int) -> Int
    @objc optional var anotherIncrement: Int { get }
}

class Counter {
    var count = 0
    var source: Source?
    func increment() {
        if let amount = source?.increment?(count: count) {
            count += amount
        } else if let amount = source?.anotherIncrement {
            count += amount
        }
    }
}

class ThreeSource: Source {
    let anotherIncrement = 3
}

class TowardZeroSource: Source {
    func increment(count: Int) -> Int {
        if count == 0 {
            return 0
        } else if count < 0 {
            return 1
        } else {
            return -1
        }
    }
}

let counter = Counter()
counter.source = ThreeSource()
for i in 1...3 {
    counter.increment()
    print(counter.count)
}

print()

counter.source = TowardZeroSource()
for _ in 1...10 {
    counter.increment()
    print(counter.count)
}

print()

counter.count = -5
for _ in 1...8 {
    counter.increment()
    print(counter.count)
}

// Print: 
// 3
// 6
// 9
//
// 8
// 7
// 6
// 5
// 4
// 3
// 2
// 1
// 0
// 0
//
// -4
// -3
// -2
// -1
// 0
// 0
// 0
// 0
```

The instance of the `Counter` use different source of the count, so that it has the different effects.

## Protocol Extensions

Protocol can be extended to provide method, initializer, subscript, and computed property implementations to conforming types. But can’t make a protocol inherit from another, the inheritance always specified in the protocol declaration itself.

```swift
extension RandomNumberGenerator {
    func randomBool() -> Bool {
        return  Double.random(in: 0...1) > 0.5
    }
}

let generator = SystemRandomNumberGenerator()
print(generator.randomBool())
```

The code above show that the class `SystemRandomNumberGeneraror` conform the `RandomNumberGenerator` protocol and we extend the `RandomNumberGenerator` protocol with a `randomBool()` method, so the `SystemRandomNumberGeneraror` can use the new method easily.

### Providing Default Implementations

We can provide **method** or **computed property** requirement in the protocol’s extension.

```swift
protocol HasLeg {
    var leg: Int { get}
    var desc: String { get }
}

extension HasLeg {
    var desc: String {
        return "I have \(leg) legs"
    }
}

class Dog: HasLeg {
    var leg: Int
    //var desc: String
    init() {
        leg = 4
    }
}
let dog = Dog()
print(dog.desc) // I have 4 legs
```

### Adding Constraints to Protocol Extensions

Here we constrain the extension of the protocol only add extension when the condition `Element: Equatable` satisfied.

```swift
extension Collection where Element: Equatable {
    func allEqual() -> Bool {
        for element in self {
            if element != self.first {
                return false
            }
        }
        return true
    }
}

let a = [100, 100, 100, 100]
let b = [100, 100, 200, 100]

print(a.allEqual())
print(b.allEqual())
```