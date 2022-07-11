---
title: "Access Control"
date: 2022-06-18T16:00:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---


Access control restrict access to part of the code form code in other source files and modules. It enable us to **hide the implement detail** of the code, and to **specify a preferred interface** which that code can be accessed and used.

Set access levels to individual types (classes, structures, enumerations), as well as to properties, methods, initializers, and subscripts belonging to those types.

Protocols can be restricted to a certain context, as can global constants, variable, and functions.

When writing a single-target App, it has no need to specify explicit access control level at all.

## Modules and Source Files

Access control model is based on the concept of modules and source files.

A module is single unit of code distribution (a framework or   ) that can be imported by another module with `import` keyword.

Each build target (app bundle or framework) is a separate module in Swift.

If an app’s code is grouped together as a stand-alone framework, everything in it will be part of a separate module.

A source file always contains definition for multiple types, functions, and so on.

## Access Levels

Swift provides five *access level* for entities. These access levels are relative to the source file in which entity is defined, and also relative to the module that source file belongs to.

- ***Open access***  and ***public access*** enable entities to be use within any source file from their defining module, and also in a source file from another module that imports the defining module.
- ***Internal access*** enables entities to be used within any source file from their defining, but not in any file outside of that module.
- ***File-private access*** restricts the use of an entity to its own defining source file.
- ***Private access*** restricts the use of an entity to the enclosing declaration, and extensions of that declaration that are in the same file.

Open access only applies to classes and class members. **It allows code outside the module to subclass and override, but public access can’t.** Making a class as open indicate that using the class from other modules as a superclass.

### Guiding Principle of Access Levels

No entity can be define in terms of another entity that has a lower(more restrictive) access level.

- A public variable can’t be defined as having an internal, file-private, or private type, because the type might not be available everywhere that the public variable is used. (higher access level can’t contain lower level in the variable)
- A function can’t have a higher access level than its parameter types and return type.

### Default Access Levels

All entities have a default access level of **internal**, so in many case we don’t need to specify an explicit access level.

### Access Level for Single-Target Apps

The code in an app is self-contained and doesn’t need to be made available outside of the app’s module. The default *internal* already matches this requirement. And we can mark some part as file private or private to hide their implementation detail from other code within the app’s module.

### Access Levels for Framework

Use default access level of internal to hide the implementation, or marked as private or file private makes the framework’s code hidden 

### Access Levels for Unit Test Targets

By default, only *open* or *public* can be access in other module. However, a unit test target can access any internal entity if use the attribute `@testable` to mark the import declaration.

## Access Control Syntax

```swift
public class SomePublicClass {}
internal class SomeInternalClass {}
fileprivate class SomeFilePrivateClass {}
private class SomePrivateClass {}

public var somePublicVariable = 0
internal let someInternalConstant = 0
fileprivate func someFilePrivateFunction() {}
private func somePrivateFunction() {}

class dog {}  // implicitly internal
let var a = 0 // implicitly internal
```

## Custom types

```swift
public var name = "default"          // explicitly public  
    var age = 0                      // implicitly internal   
    fileprivate func run() {}        // explicitly file-private
    private func eat() {}            // explicitly private
}

class Cat {                      // implicitly internal
    var name = ""                // implicitly internal
    fileprivate func sit() {}    // explicitly file-private
    private func sleep() {}      // explicitly private
}

fileprivate class Bear {       // explicitly file-private
    func climb() {}            // implicitly file-private
    private func stand() {}    // explicitly private
}

private class Snake {    // explicitly private
    func eat() {}        // implicitly private
}
```

### Tuple Types

When compound tuple, it will get the minimum access level.

If we compound two tuple type, one with internal access and one with private access, the result will be private. A tuple type’s access level is determined automatically from the types that make up the tuple type, and can’t be specified explicitly.

### Function Types

The function’s access level must matches the access level of the return type.

Here the tuple has the minimum access level (private), so the function should be marked as `private`.

```swift
private func someFunc() -> (SomeInternalClass, SomePrivateClass) {

}
```

### Enumeration Types

Each cases will automatically receive the same access level as the enumeration it belongs to.

```swift
public enum Direction {
    case north  // public
    case south  // public
    case east
    case west
}
```

Raw values and associated values must have an access level at least as high as the enumeration’s level.

The access level of a nested type is the same as its containing type, unless the containing type is public (Nested type define within a public type have an automatic access level of internal implicitly). 

## Subclassing

A subclass can’t have higher access level than its superclass.

For classes that are defined in the same module, we can override any class member.

For classes that are defined in another module, we can override any open class member.

An override can make an inherited class member more accessible than its superclass version.

```swift
public class A {
    fileprivate func someMethod() {
        
    }
}

public class B: A {
    override internal func someMethod() {
        super.someMethod()   // Because superclass A and subclass B are defined in the same source file, it's valid for the method in B to call super.someMethod.
    }
}
```

It’s valid for a subclass member to call a superclass member that has lower access permission than the subclass member, as long as the call to the superclass’s member has the allowed access level context.

```swift
public class A {
    fileprivate func someMethod() {
        
    }
}

public class B: A {
    override internal func someMethod() {
        super.someMethod()
    }
}

// They are in the same source file, so the "B" can call the file-private method from the "A".
```

## Constants, Variables, Properties, and Subscripts

A private type can’t contain a property has more public access level.

For example, if a constant, variable, property, or subscript makes use of a private type, the constant, variable, property or subscript must also be marked as private:

```swift
private var privateInstance = SomePrivateClass()
```

### Getters and Setters

It automatically receive the same access level from the constant, variable, property, or subscript that it belongs to.

And the *setter* and *getter* can be marked with a lower access level than the access level that get from the upper context. Use `fileprivate(set)`, `private(set)`, and `internal(set)` to mark a lower access level for the setter.

```swift
struct Track {
    private(set) var number = 0 // the setter can only be used inside the current structure.
    var value: String = "" {
        didSet {
            number += 1
        }
    }
}

var track = Track()
track.value = "I "
track.value += "read "
track.value += "books."
print("Modify times: \(track.number)  String: \(track.value)")
// Print:
// Modify times: 3  String: I read books.
```

## Initializers

Custom initializers can be assigned an access level less than or equal to the type that they initialize. However, A required initializer must have the same access level as the class it belongs to.

The type of an initializer’s parameters can’t be more private than the initializer’s own access level.

### Default Initializers

A default initializer has the same access level as the type it initializes, unless that type is `public`. If the type is `public`, the default initializer will be `internal` as default.

```swift
fileprivate class Dog {
    // It's default initializer is fileprivate
    var name: String?
}
fileprivate var dog = Dog()
```

```swift
public class Cat {
    // It's default initializer's access level is internal
    var name: String?
}
var cat = Cat()
```

If we want a no-argument initializer (in public type) be used in another module, we must explicitly provide a `public` no-argument initializer in the type definition. Because the `init()` will be internal in default.

```swift
public class Cat { internal
    var name: String?
    public init() {
        
    }
}
```

### Default **Member-wise**  Initializers for Structure Type

Once the structure’s stored properties are private, the structure type will be considered as private. Likewise, The file-private has the same situation.

As with the default initializer above, if the structure is public, the initializer of it is internal in default. So if we want to use the no-argument initializer in other modules, we must write the  The `public init()` implementation explicitly.

## Protocol

**The requirement in the protocol keep a same access level as the protocol.**

We can’t set a protocol requirement to a different access level than the protocol it support. This ensures that all of the protocol’s requirement will be visible on any type that adopts the protocol.

If we define a public protocol, the protocol’s requirements require a public access level for those requirements when they’re implemented.

### Protocol Inheritance

The sub-protocol have at most the same access level as the the super-protocol. For example a public protocol can’t inherit from an internal protocol.

### Protocol Conformance

A type can conform a protocol with a lower access level than the type itself.

For example, if a type is public, but a protocol it conforms is internal, the type’s conformance to that protocol is also internal. And the type’s implementation of each protocol requirement must be at least internal. 

If the protocol is with a private, the implementation should be at least file-private.

```swift
private protocol Action { 
    func run()
    func eat()
}

public class Pig: Action {
    // It's not allowed, it must be declared as file-private because it matches a requirement in private.
    private func run() {
        print("run")
    }
    // It's OK to mark it as fileprivate.
    fileprivate func eat() {
        print("eat")
    }
}

public class Goat: Action {
    
    // It's internal in default
    func run() {  
        
    }
    
    // Marked as public explicitly.
    public func eat() {
        
    }
}
```

## Extensions

If the extension extends a public or internal type, any new member in the extension will be an internal access level in default.

If the extension extends a file-private type, any new member in the extension will be a file-private access level in default.

Alternatively, we can mark the extension with an access level to let the member inside extension to have new access level.

```swift
// It can only accessed from the same file.
fileprivate class Shape {
    var width: Int?
    var height: Int?
}

// It will be can only accessed inside the current context.
private extension Shape {
    var area: Int {
        if let x = width , let y = height {
            return x * y
        } else {
            return 0
        }
    }
}

```

We can’t provide an explicit access-level modifier for an extension i**f we’re using that extension to add protocol conformance**.

```swift
// It's with internal access level in default.s
protocol Calculator {
    func calcuArea() -> Int
}

fileprivate class Shape {
    var width: Int?
    var height: Int?
}

// Here the extension can't explicit access-level modifier.
extension Shape: Calculator{
    func calcuArea() -> Int {
        if let x = width , let y = height {
            return x * y
        } else {
            return 0
        }
    }
    
}
```

### Private Members in Extensions

We can:

- Declare a private member in the original declaration, and access that member from extensions in the same file.
- Declare a private member in one extension, and access that member from another extension in the same file.
- Declare a private member in an extension, and access that member from the original declaration in the same file.

Example:

```swift
protocol Printer {
    func printLegs()
}

struct Cat {
    private var leg = 4
}
extension Cat: Printer {
    func printLegs() {
        print(leg)
    }
    
}
```

## Generics

It will be the minimum of the access level on the type parameter.

## Type Aliases

A type alias can have an access level **less than or equal** to the access level of the type it aliases.