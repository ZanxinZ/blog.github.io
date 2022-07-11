---
title: "Subscripts"
date: 2022-04-10T16:00:40+08:00
ShowToc: true
summary: "Set subscript(index: Int) in the class to index the data of the instance of the this class."
tags:
- 编程语言
categories:
- Swift
---


- A shortcut for accessing the member of a collection, list, or sequence.
- Classes, structures, and enumerations can define subscripts.
- Get result by square brackets `[]` .
- Use the `subscript` keyword.

We can define multiple subscripts for a single type, and the appropriate subscript overload to use is selected based on the type of index value you pass to the subscript.

## Syntax

```swift
subscript(index: Int) -> Int {
		get {}
		
		set(newValue) {}
}

subscript(index: Index) -> Int {
		// return directly
}

```

### Example

```swift
struct TimesTable {
		let mul: Int
		subscript(index: Int) -> Int {
				return mul * index
		}
}

let times = TimesTable(mul: 3)

print(times[6])
```

## Usage

```swift
class Order {
    var names: [String] = []
    subscript(index: Int) -> String {
        assert(index < names.count, "out of index")
        return names[index]
    }
}

var order = Order()
order.names.append("Mike")
order.names.append("Amy")
order.names.append("John")

print(order[1])   // Amy
```

The `Dictionary` has implements the subscript. Not all key has value, so the value is optional.

```swift
var numberOfLegs = ["cat": 4, "bird": 2, "dog": 4]

print(numberOfLegs["dog"]!)  // The result of the subscript is optional, here force unpack.
```

Delete the value by assign it to `nil` .

```swift
numberOfLegs["dog"] = nil
```

## Subscript Options

Subscript can:

- take any number and any type of input parameters.
- return a value of any type.
- take a varying number of parameter and provide default values.

Subscript can’t:

- use in-out parameters.

### Subscript Overloading

The subscript defined with different count of parameters or different type of parameters.

```swift
struct Matrix {
    let rows: Int, columns: Int
    var grid: [Double]
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(repeating: 0.0, count: rows * columns)
    }
    func indexIsValid(row: Int, column: Int) -> Bool {
        return row >= 0 && row < rows && column >= 0 && column < columns
    }
		
		// Here is the overloading
    subscript(row: Int, column: Int) -> Double {
        get {
            assert(indexIsValid(row: row, column: column))
            return grid[row * columns + column]
        }
        set {
            assert(indexIsValid(row: row, column: column))
            grid[row * columns + column] = newValue
        }
    }

		// Here is the overloading
    subscript(index: Int) -> Double {
        get {
            assert(index < grid.count)
            return grid[index]
        }
        set {
            assert(index < grid.count)
            grid[index] = newValue
        }
    }
}

var mat = Matrix(rows: 4, columns: 5)
print(mat)
mat[1,1] = 2.5
print(mat)
mat[5] = 7.7
print(mat)
```

## Type Subscripts

The subscripts show above are instance subscript. Now let’s learn about the type subscripts.

- Use `static` keyword to indicate the type subscript within the classes, structures and enumerations.
- When define type subscript in the class, Use `class` keyword instead of `static` to allow the subclass to override the subscripts.

```swift
enum Day: Int {
    case Monday = 1, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday
    static subscript(n: Int) -> Day {
        return Day(rawValue: n)!
    }
}

let day = Day[3]
print(day)
```