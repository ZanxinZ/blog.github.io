---
title: "Concurrency"
date: 2022-05-08T16:00:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---

Swift has built-in support for writing asynchronous and parallel code in a structured way.

Parallel code means multiple pieces of code run simultaneously.

- Concurrency will make the code harder to debug.
- Swift can help to catch problem at compile time.

Although it’s possible to write concurrent code without using Swift language support, that code tends to be hard to read.

```swift
listPhotos(inGallery: "Summer Vacation") { photoNames in
    let sortedNames = photoNames.sorted()
    let name = sortedNames[0]
    downloadPhoto(named: name) { photo in
        show(photo)
    }
}
```

Keywords:

- `async` make the function asynchronous.
- `await` make function calling synchronous.

## Defining and Calling Asynchronous Functions

The asynchronous functions or methods can run in another new ‘thread’, but we have no need to create a thread to do that, the Swift manage the asynchronous calling automatically.

```swift
func listPhotos(inGallery name: String) async -> [String] {
    let result = // ... some asynchronous networking code ...
    return result
}
```

With the `async` keyword, the function will be executed in an asynchronous way when it’s been called (the `async` should be written  before `throws`). The network request takes a relatively long time, so use the we `async`  to let the rest of the app’s code keep running while this code waits for the picture to be ready.

### Suspend Execution by `await`

Use `await` to make the caller being suspend. Waiting for the return of the function.That code also runs until the next suspension point, marked by `await`, or until it completes.

While this code’s execution is suspended, some other concurrent code in the same program runs. For example, maybe a  long-running background task continues updating a list of new photo galleries.

```swift
let photoNames = await listPhotos(inGallery: "Summer Vacation")
let sortedNames = photoNames.sorted()
let name = sortedNames[0]
let photo = await downloadPhoto(named: name)
show(photo)
```

The `await` make the code to be suspend that also called *yielding the thread,* so behind the scenes, the other code in the same thread will have opportunity to be executed.

Only certain place in the program can call asynchronous function or methods:

- Code in the body of an asynchronous function, method, or property.
- Code in the static `main()` method of a structure, class, or enumeration that mark with `@main` .
- Code in an unstructured child task.

A way to simulate the waiting for network request.

```swift
func listPhotos(inGallery name: String) async throws -> [String] {
    let result = ["fine", "aaa"]
    try await Task.sleep(nanoseconds: 5 * 10000) // Use the 'try await Task.sleep' to simulate
    
    print("did")
    return result
}

func downloadPhoto(names: [String]) async throws {
    print("download")
    try await Task.sleep(4 * 1_000_000)
}
```

## Asynchronous Sequences

Suspend every execution of the elements.

```swift
import Foundation

let handle = FileHandle.standardInput
for try await line in handle.bytes.lines {
    print(line)
}
```

## Calling Asynchronous Functions in Parallel

In synchronous way, they are processed one by one.

```swift
let firstPhoto = await downloadPhoto(named: photoNames[0])
let secondPhoto = await downloadPhoto(named: photoNames[1])
let thirdPhoto = await downloadPhoto(named: photoNames[2])
```

In asynchronous way, they are processed asynchronously, don’t wait for each mother.

```swift
async let firstPhoto = downloadPhoto(named: photoNames[0])
async let secondPhoto = downloadPhoto(named: photoNames[1])
async let thirdPhoto = downloadPhoto(named: photoNames[2])

// Async can't be used for the non-async properties.
```

- Both `await` and `async`-`let` allow other code to run while they’re suspended.
- The code can use both `await` and `async-let` in a same project.

## Tasks and Task Groups

### Structured concurrency

- Tasks are arranged in a hierarchy. Each task in a task group has the same parent task, and each task can have child tasks.

```swift
await withTaskGroup(of: Data.self) { taskGroup in
    let photoNames = await listPhotos(inGallery: "Summer Vacation")
    for name in photoNames {
        taskGroup.addTask { await downloadPhoto(named: name) }
    }
}
```

### Unstructured Concurrency

Two way to create unstructured task and get a task handle as the result. 

- To create an unstructured task that runs on the current actor, call the `Task.init(priority: operation:)` initializer.
- To create an unstructured task that’s not part of the current actor, call the `Task.d etached(priority:operation:)` class method.

```swift
let newPhoto = // ... some photo data ...
let handle = Task {
    return await add(newPhoto, toGalleryNamed: "Spring Adventures")
}
let result = await handle.value
```

### Task Cancellation

Task check can do some responds when the condition is satisfied. It depended on the work we do.

- Throwing error like `cancellationError`
- Returning `nil` or an empty collection
- Returning the partially completed work

Call `Task.checkCancellation()` , and it will throws `CancellationError` if the task has been cancelled. Call `Task.isCancelled` and handle the cancellation in the code.

Call `Task.cancel()` to propagate cancellation manually.

## Actors

It’s similar to the `class`. It’s reference type. **Allow only one task to access their mutable state at a time.**

When a task(A) is interacting with  an actor instance, the another task(B) can’t  access this actor instance at the same time, and the task B will be suspend and waiting for access the property.

```swift
actor Home{
    let label: String
        var t: [Int]
        private(set) var max: Int = 0
        init(label: String, t: Int) {
            self.label = label
            self.t = [t]
            if t > self.max {
                self.max = t
            }
        }
}

extension Home{
    func update(with temp: Int) {
        t.append(temp)
        if temp > max {
            max = temp
        }
    }
}
```

Use the actor

```swift
@main
struct DemoApp {
    
    static func main() async {
        var home = Home(label: "Ha", t: 10)
        print(await home.max)
        await home.update(with: 22)
        print(await home.max)
        
    }
}
```