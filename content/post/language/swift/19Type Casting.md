---
title: "Type Casting"
date: 2022-05-16T16:00:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---

Type casting in Swift is implemented with `is` and `as` operators.

Type casting:

- A subclass instance can be use as a superclass instance.

## Defining a Class Hierarchy

```swift
class Media {
    var name: String
    init(name: String) {
        self.name = name
    }
}

class Song: Media {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}

class Movie: Media {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}

var library = [
    Movie(name: "A", director: "Michael"),
    Song(name: "B", artist: "Elvis"),
    Movie(name: "C", director: "Orson"),
    Song(name: "D", artist: "Rick"),
    Media(name: "E")
]
// The "library" is inferred to be [Media] 

for item in library {
    print(item.name)  // Here the item is of "Media" type 
}

// If we want to use the library's item as the subclass instance, we should downcast the item.
```

## Checking Type

Use `is` to check whether an instance is of a certain subclass type.

```swift
for item in library {
    if item is Movie {
        print(item.name + " is Movie")
    } else if item is Song {
        print(item.name + " is Song")
    } else {
				print(item.name + " is Media")
		}
}
```

When set the movies and songs to the `library`, it's the automatically upcast. **The upcast is a type of polymorphism.** 

## Downcasting

When we believe that a superclass instance is actually of a subclass type, we can downcast the instance to be a subclass type.

Two way to downcast a instance: 

- `as?` get an optional result. (Recommended)
- `as!` force downcast, it will trigger runtime error when the types not match.

```swift
for item in library {
    if let movie = item as? Movie {
        print(movie.name + " " + movie.director)
    } else if let song = item as? Song {
        print(song.name + " " + song.artist)
    }
}

// Print:
// A Michael
// B Elvis
// C Orson
// D Rick
// E is just a Media
```

## Type Casting for Any and AnyObject

- `Any` can represent an instance of any type at all, including function type, including class type.
- `AnyObjece` can represent an instance of any class type.

```swift
var things: [Any] = []

things.append(0)
var a: Int? = 3
things.append(a as Any)
things.append(4.0)
things.append(42)
things.append("Oh")
things.append((2.0, 3.5))
things.append(Media(name: "LoL"))

things.append({(name: String) -> String in "Hello, \(name)"})

for thing in things {
    switch thing {
    case is Int:
        print("\(thing) is Int")
    case let someDouble as Double where someDouble > 2:
        print("Double \(someDouble) is greater than 2")
    case is Double: 
        print("\(thing) is Double")
    case let s as String: 
        print("\(thing) is String")
    case let (x, y) as (Double, Double):
        print("x: \(x) y: \(y)")
    case let media as Media: 
        print("Media: \(media.name)")
    case let say as (String) -> String:
        print("Func: \(say("John"))")
    default: break
        
    }
}

// Print:
// 0 is Int
// Optional(3) is Int
// Double 4.0 is greater than 2
// 42 is Int
// Oh is String
// x: 2.0 y: 3.5
// Media: LoL
// Func: Hello, John
```

If we want to use an optional variable as `Any` , write `as Any` , otherwise 

Swift will give us a warning.

 

```swift
var a: Int? = 3
things.append(a)        // Warning
things.append(a as Any) // No warning
```