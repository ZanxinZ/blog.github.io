---
title: "Inheritance"
date: 2022-04-24T16:00:40+08:00
ShowToc: true
summary: A class inherit another class call super class, and get some ability from super class.
tags:
- 编程语言
categories:
- Swift
---


Class A inherit from class B.  Then A is the so call subclass, B is the superclass.

Subclass can do:

- Access methods, properties, and subscripts belonging to their superclass,
- Override the methods, properties and subscripts.
- Extends the functionality of the superclass.

Superclass can do:

- Add property observer to inherited properties. (In order to be notified when the value of property changes)

## Usage

```swift
class Card {
    var number: Int?
    func changeNumber(number: Int) -> Bool {
        self.number = number
        return true
    }
}

// Inherit from Card
class StudentCard: Card {
    var studentID: Int?
}

// Inherit from Card
class BankCard: Card {
    var account = 0
}

var studentCard = StudentCard()
studentCard.number = 123
studentCard.studentID = 220022
print(studentCard.number!)
print(studentCard.studentID!)

var bankCard = BankCard()
bankCard.changeNumber(number: 124)
bankCard.account = 100
print(bankCard.number!)
print(bankCard.account)
```

## Overriding

A subclass can provide its own custom implementation of an instance method, type method, instance property, type property, or subscript that it would otherwise inherit from a superclass. 

### Accessing Superclass Methods, Properties, and Subscripts

Use the `super` keyword to access the things belong to the superclass.

Overriding the initialization.

```swift
class Card {
    var number: Int?
    var initFee = 0
    init(number: Int){
        self.number = number
    }
}

class StudentCard: Card {
    var studentID: Int?
    
    // Override the initialization from the superclass
    override init(number: Int) {
        super.init(number: number)  // It need to init the superclass at first, the 'super' can't be omitted here.
        super.initFee = 15          // The 'super' can be omitted here. 
    }
    
}

var studentCard = StudentCard(number: 123)

print(studentCard.initFee)   // 15
```

### Overriding Methods

```swift
class Card {
    var number: Int?
    var initFee = 0
    func setInitFee(fee: Int) {
        self.initFee = fee
    }
    init(number: Int){
        self.number = number
    }
}

class StudentCard: Card {
    var studentID: Int?
    
    override func setInitFee(fee: Int) {
        super.initFee = fee + 5
    }
}

var studentCard = StudentCard(number: 123)
studentCard.setInitFee(fee: 5)
print(studentCard.initFee)   // 10
```

### Overriding Properties

Regardless of whether the property is stored or computed, override an inherited instance or type property to provide custom **getter** and **setter**, or to add property observer for the property.

The subclass doesn’t know whether the inherited property is stored or computed.(So must state both the name and the type of the property when override it)

Read-Write and Read-Only:

- We can override a read-only property to be read-write, by providing getter and setter.
- We can’t override a read-write property to be read-only.
- When override the a setter, must override the getter at the same time.
- Return `super.someProperty` simply when override getter only.

The code below show how to override a property from superclass.

```swift
class Card {
    var number: Int?
    var _desc: String = ""  // The setter can't set to itself, so use '_desc' to store the value.
    var desc: String {
        get {
            return _desc + "Has a number \(number!). "
        }
    }
}

class StudentCard: Card {
    var initFee = 0
    override var desc: String {
        get { return super.desc + " With a initFee \(initFee)"}
        set {
            _desc = "[" + newValue + "]"
        }
    }
}

var studentCard = StudentCard()
studentCard.number = 1234
studentCard.initFee = 15
studentCard.desc = "A student"

print(studentCard.desc)
// Print: [A student]Has a number 1234.  With a initFee 15
```

### Overriding Property Observers

- can’t add property observers to inherited constant stored properties or inherited read-only computed properties.
- can’t provide both overriding setter and overriding property observer for the same property.

The code below show the VipStudentCard inherit from the StudentCard and add an observer to the desc.

```swift
class Card {
    var number: Int?
    var _desc: String = ""  
    var desc: String {
        get {
            return _desc + "Has a number \(number!). "
        }
    }
}

class StudentCard: Card {
    var initFee = 0
    override var desc: String {
        get { return super.desc + " With a initFee \(initFee)"}
        set {
            _desc = "[" + newValue + "]"
        }
    }
}

class VipStudentCard: StudentCard {
    override var desc: String {
        didSet {
            initFee += 5
        }
				
    }
}

var vip = VipStudentCard()
vip.number = 12345
vip.initFee = 15
vip.desc = "Vip"
print(vip.desc)
// Print: [Vip]Has a number 12345.  With a initFee 20
```

Or simply observe any value changes from within the custom setter for the desc in the `VipStudentCard` .

```swift
class Card {
    var number: Int?
    var _desc: String = ""  
    var desc: String {
        get {
            return _desc + "Has a number \(number!). "
        }
    }
}

class StudentCard: Card {
    var initFee = 0
    override var desc: String {
        get { return super.desc + " With a initFee \(initFee)"}
        set {
            _desc = "[" + newValue + "]"
        }
    }
}

class VipStudentCard: StudentCard {
    override var desc: String {
        set{
            _desc = "{" + newValue + "}"
            initFee += 5
        }
        get {
            return super.desc
        }
        
    }
}

var vip = VipStudentCard()
vip.number = 12345        // The number property is from the base class 'Card'
vip.initFee = 15          // The initFee
vip.desc = "Vip"
print(vip.desc)
// Print: {Vip}Has a number 12345.  With a initFee 20
```

## Preventing Overrides

We can prevent the method, property, or subscript from being overridden by marking it with `final` keyword.

```swift
class Card {
    var number: Int?
    var _desc: String = ""
		// Here the desc can't be overridden, because it's final.
    final var desc: String {
        get {
            return _desc + "Has a number \(number!). "
        }
        set {
            _desc = "|" + newValue + "|"
        }
    }
}

class StudentCard: Card {
    var initFee = 0
    
}

class VipStudentCard: StudentCard {
}

var vip = VipStudentCard()
vip.number = 12345
vip.initFee = 15
vip.desc = "Vip"
print(vip.desc)
// Print: |Vip|Has a number 12345.
```

If we mark the entire class as final,  it can’t be inherited.

```swift
final class Card {
    var number: Int?
    var _desc: String = ""
    var desc: String {
        get {
            return _desc + "Has a number \(number!). "
        }
        set {
            _desc = "|" + newValue + "|"
        }
    }
}

// class StudentCard: Card {} // Compile-time error.
```