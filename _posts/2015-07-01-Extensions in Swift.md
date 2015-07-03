---
layout: post
title: Extensions in Swift
categories: [Development]
tags: [Swift]
description: Có thể hiểu extension trên swift là bao hàm category và extension trên ObjC.
---

Nếu có làm việc trên ObjC thì chắc bạn đã làm quen qua *category* và *extension*. Khi chuyển qua swift, thì khái niệm category không còn dùng nữa, toàn bộ đều là extension. Có thể hiểu extension trên swift là bao hàm category và extension trên ObjC.

###Sử dụng để làm gì?
- thêm các computed properties 
{% highlight swift %}
extension Double {
    var km: Double { return self * 1_000.0 }
    var m: Double { return self }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// prints "One inch is 0.0254 meters"
{% endhighlight %}
- thêm các methods 
- thêm new initializers 
- định nghĩa subscripts 
{% highlight swift %}
extension Int {
    subscript(var digitIndex: Int) -> Int {
        var decimalBase = 1
        while digitIndex > 0 {
            decimalBase *= 10
            --digitIndex
        }
        return (self / decimalBase) % 10
    }
}
746381295[0]
// returns 5
746381295[1]
// returns 9
{% endhighlight %}
- định nghĩa và sử dụng new nested types 
- thêm các func vào existing type để dễ sử dụng
- hay sử dụng để gắn một hay nhiều protocols
{% highlight swift%}
extension SomeType: SomeProtocol, AnotherProtocol {
    // implementation of protocol requirements goes here
}
{% endhighlight %}

Lưu ý:
- không thể override các hàm đã tồn tại 
- các func trong extension không bị giới hạn trong scope như trong objC. Nếu định nghĩa 1 hàm trong 1 extension, thì nó có thể dùng ở tất cả các extension khác.
- không thể thêm các thuộc tính để lưu trữ (stored properties) hay các thuộc tính để quan sát thay đổi của các thuộc tính đã tồn tại (property observers to existing properties)
- Khi thêm *constructor* nên gọi hàm constructor từ class chính để đảm bảo các biến đều được khởi tạo 
- sử dụng *mutating* keyword nếu có thay đổi self 
{% highlight swift %}
extension Int {
    mutating func square() {
        self = self * self
    }
}
var someInt = 3
someInt.square()
// someInt is now 9
{% endhighlight %}

Tham khảo: [The Swift Programing Language](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html#//apple_ref/doc/uid/TP40014097-CH24-ID151)
