---
title: "Strings and Characters"
date: 2022-03-20T16:04:40+08:00
ShowToc: true
tags: 
- 编程语言
categories: 
- Swift
---


String type is bridged with Foundation’s NSString. Foundation extends String to expose methods defines by NSString. If import Foundation, you can access those NSString methods on String without casting.

## String Literals

```swift
// "Hello World!" is a String literal. 
let sentence = "Hello World!"

// Multiline
let story = """
There are some people in the room.
They are having a party.
Because today is the Christmas.
"""
// every line has the line breaks.
// the start and end sign(""") must take a single line.

// backlash(\), it means that the string is not broken. Then line breaks not to be part of the string's value.
let content = """
In a happy atmos\
phere. We start the conversation
"""
print(content) // result: In a happy atmosphere. We start the conversation.

// Alignment of quotation marks in the multiline string.
let desc = """
    Ha it's so funny.
"""
print(desc)  // result:     Ha its so funny.

let desc = """
    Ha it's so funny.
    """
print(desc)  // result: Ha its so funny.

let desc = """
        Ha it's so funny.
    """
print(desc)  // result:     Ha its so funny.

```

Special Character in String Literals

| \0 | null character |
| --- | --- |
| \\ | backslash |
| \t | horizontal tab |
| \n | line feed |
| \r | carriage return |
| \” | double quotation mark |
| \' | single quotation mark |
| \u{n} | n is a 1-8 digit hexadecimal |

```swift
let dollarSign = "\u{24}"    // $

let blackHeart = "\u{2665}"  // ♥

let redHeart = "\u{1f496}"   // 💖

// Because multiline string literals use three double quotation marks, one double quotation can be used in multiline string litral without escaping it.
// And should escape the three double quotation which inside the multiline string literal.

let marks = """
Escaping the first quotation mark \"""
Escaping all three quotation \"\"\"
"""

// Abosultly string
// use the #""" and """# to include a absultly pure string.
let words = #"""
    It sounds very good."""
    """#
print(words)  // result: It sounds very good."""

// use the #" and "# to include a absultly pure string.
let words = #"It sounds """ very good."#
print(words)  // result: It sounds """ very good.

// So it has no need to add escaping character(\).

// If we want to cancel the effect of '#' in a string, use another '#' near the character.
let otherWords = #"hello \#rworld"#
print(otherWords)  // result: 

// If we want to cancel the effect of '#' in a multiline string, use another '#' near the character.
let otherWords = #"""
hello \###rworld
"""#
print(otherWords)  // result: hello     world 
```

Initializing an Empty String 

```swift
// They are all empty, they are equivalent to each other.
var myName = ""
var hisName = String()

// Find out if the string is empty.
if myName.isEmpty {
    print("It's empty.")
}
```

## String Mutability

```swift
var a = "Horse "
var b = "carriage"
b = a + b     // it willf create a new String and assign to b

let str = "he"
str += "ran"  // it will trigger the compile-time error.
// constant string cannot be modified.
```

## Strings Are Value type

If we create a String value, that String value is **copied** when it passed to a function or method. When it’s passed, it’s not the original version.

You can be confident that the string you are passed won’t be modified unless you modify it yourself.

Swift’s compiler optimizes string usage so that actual copying takes place only when absolutely necessary.

## Character

```swift
for character in "dog!" {
    print(character)
}

let c : Character = "!"

let characters : [Character] = ["c", "a", "t", "s"]
let str = String(characters)

// concatenation 
str.append(c)

let pet = "dog " + "cat" 

var name = "Mike "
name += "Amy"

// multiline string
let start = """
one
two
"""
let end = """
three
"""
let goodStart = """
one
two

"""

print(start + end)
// result: 
// one
// twothree

print(goodStart + end)
// result:
// one 
// two
// three
```

## String Interpolation

```jsx
let value = 3
let message = "The value is \(value). Four times is \(value * 4). \("It's " + "fine.")"
print(message)  // result: the value is 3. Four times is 12. It's fine.

print(#"It's a \(value)."#)  
// It will print the literal string without escaping the 'value'.
// It's a \(value).

print(#"It's a \#(value)."#)
// It will escape the value.
// It's a 3.
```

## Unicode

```swift
print("\u{E9}")      // é
print("\u{65}\u{301} // é  // two character compound into one character automatically.

print("\u{1f425}")   // 🐥

let c = "\u{1F1E8}\u{1F1F3}" 
print(c + String(c.count)  // 🇨🇳1  // two character compound into one. cn = c + n
```

## Counting Character

```swift
let str = "chick\u{1f425}"
print(str + String(str.count))    // result: chick🐥6

var word = "cafe"
word += "\u{301}"
print(word + String(word.count))  // result: café4
```

## Accessing and Modifying a String

Accessing

1. str.startIndex
    
     The startIndex is 0.
    
2. str.endIndex
    
     **The endIndex is equals to count, it points to the next to the last element.**
    

Index is a class. **It’s special, just like a pointer. It often not start at 0.**

- str.index(before : String.Index)
- str.index(at : String.Index)
- str.index(after : String.Index)

```swift
var str = "peak"
str[str.startIndex] = "g" // not allowed, can not assign through subscript
print(str[str.startIndex] // subscript is get-only, the result is : p
print(str[str.index(before: str.endIndex)])      // result: k
print(str[str.index(after: str.startIndex)])     // result: e
print(str[str.index(str.startIndex, offsetBy:2)])// result: a

for c in str.indices {
	print("\(str[c]) ", terminator: "") // set terminator empty to cancel the line feed.
}
// result: p e a k
```

Inserting and removing

- word.insert(_: Character, at: String.Index)
- word.remove(at: String.Index)
- message.insert(contentsOf: Collection, at: String.Index)
- message.removeSubrange(_: Range<String.Index>)

```swift
// Insert and remove single character
var word = "world"
word.insert("k", at: word.index(word.endIndex, offsetBy: -2)) 
word.remove(at: word.index(word.startIndex, offsetBy: 4))
word.remove(at: word.index(before: word.endIndex))
print(word)
// result: work

// Insert and remove a substring
var message = "we"
message.insert(contentsOf : "lcome to here", at : message.endIndex)
print(message)  //print: welcome to here 

message.removeSubrange(message.index(message.startIndex, offsetBy: 7)..<message.endIndex)

print(message)  //print: welcome
```

Substrings

String is value type, it’s stable but not efficient when cut the string to get substring.

Substring is a type. Just to reference the Substring inside the whole String.

```swift
var str = "Hello, World!"
let index = str.firstIndex(of: ",") ?? str.endIndex
var sub = str[..<index]    // It doesn't has a whole copy of String, just create a new type 'Substring' to reference the part we cut.
print(sub)

sub.remove(at: sub.startIndex)
print(sub)     // print: ello
print(str)     // It doesn't affect the origin String, because 'sub' is a independent Substring instance.

let newString = String(sub)// Create an instance of String. Transform a subString to String for long term keep.
```

## Comparing Strings

```swift
var a = String("word")
var b = String("word")
print(a == b)         // print: true

var str = String("Hello, world!\u{E9}")
var quote = String("Hello, world!\u{65}\u{301}")
if str == quote {
    print("Equal") 
    // The combine result of 'quote' is same as in str. So the these two Strings are equal.
    // e +  ́ = é
}
// print: equal

// Single character
var c = String("\u{41}")
var d = String("\u{041}")
print(c == d)            // print: true

var c = String("\u{41}")
var d = String("\u{0410}")
print(c == d)           // print: false

```

## Prefix and Suffix Equality

The `hasPrefix(_:)` and `hasSuffix(_:)` methods perform a character-by-character canonical equivalence comparison between the extended grapheme clusters in each string.(character one by one compare)

```swift
var sentences = [
    "Hello, world!",
    "Hello, friend!",
    "Hi,Hello, friends all!",
    "Welcome, friend!",
    "Welcome, friends all!"
]
for sentence in sentences {
    if sentence.hasPrefix("Hello") {
        print(sentence)
    }
}
// Hello, world!
// Hello, friend!

for sentence in sentences {
		if sentence.hasSuffix("friend!") {
        print("[" + sentence + "]")
    }
}
// [Hello, friend!]
// [Welcome, friend!]     

```

## Unicode Scalar Representation

Unicode \u{1F436} each character has 20bit. 0—1048575

UTF-16 each character has 16bit, 2Byte. 0—65535

UTF-8 each character has 8bit, 1Byte. 0—255

```swift
let word = "dog!!\u{1F436}"

print(word)
print("UTF-8:", terminator: " ")
for c in word.utf8 {
    print("\(c)", terminator : " ")
}
print("\nUTF-16",terminator: " ")
for c in word.utf16 {
    print("\(c)", terminator: " ")
}
print("\nUnicode:", terminator: " ")
for c in word.unicodeScalars {
    print("\(c.value)", terminator: " ")
}
print()
print(word.count)
```

The execute result

dog!!🐶
UTF-8: 100 111 103 33 33 240 159 144 182
UTF-16 100 111 103 33 33 55357 56374
Unicode: 100 111 103 33 33 128054
6