---
title: "Nested Types"
date: 2022-05-16T16:05:40+08:00
ShowToc: true
tags:
- 编程语言
categories:
- Swift
---

Enumerations, classes or structures can be nested in another type.

```swift
struct Closh {
    enum Size: String{
        case H = "high", M = "Medium", L = "Low"
    }
    
    enum Detail: Int {
        case H = 180, M = 170, L = 160
        struct Price {
            let normal: Int, discount: Int?
        }
        var price: Price {
            switch self {
            case .H:
                return Price(normal: 100, discount: 90)
            case .M:
                return Price(normal: 90, discount: 80)
            case .L:
                return Price(normal: 80, discount: 70)
            default:
                return Price(normal: self.rawValue, discount: nil)
            }
        }
    }
    
    let size: Size, detail: Detail
    var description: String {
        return "\(size.rawValue), PriceNormal: \(detail.price.normal) PriceDiscount: \(detail.price.discount)"  
    }
}

var closh = Closh(size: .M, detail: .M)
print(closh.description)
// Print: Medium, PriceNormal: 90 PriceDiscount: Optional(80)
```

Referring to Nested Types

```swift
var L = Closh.Size.L.rawValue
print(L)     // Low
```