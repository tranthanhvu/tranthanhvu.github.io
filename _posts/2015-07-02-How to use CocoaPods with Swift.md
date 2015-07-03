---
layout: post
title: How to use CocoaPods with Swift
categories: [Development]
tags: [Swift]
description: CocoaPods dùng để quản lý các thành phần liên quan (cụ thể là lib, framework) cho project cocoa.
---

Có thể bạn đã từng làm việc hoặc nghe qua về Cocoapods, Chưa nghe thì cũng chả sao :D CocoaPods dùng để quản lý các thành phần liên quan (cụ thể là lib, framework) cho project cocoa.

###Tại sao nên dùng cocoapods
Thông thường khi bạn viết code có sử dụng các thư viện bên ngoài, một trong các việc khó khăn và cũng ít developer làm đó là quản lý các version của các thử viện đó. May mắn là cocoapods được sinh ra để làm chuyện này. Nó giúp:

- cập nhận libs (một số libs cần phải cập nhật chung vì có liên quan đến nhau)
- quản lý version cho libs
- tìm các libs hữu ích từ [central location](https://cocoapods.org/)

Nếu có làm việc trên ObjC thì chắc bạn đã làm quen qua **category** và **extension**. Khi chuyển qua swift, thì khái niệm category không còn dùng nữa, toàn bộ đều là extension. Có thể hiểu extension trên swift là bao hàm category và extension trên ObjC.

###Setup cocoapods 
Các bước cài đặt Cocoapods (thực hiện trên terminal, ai không biết terminal thì tự tìm hiểu nhé :v )

- cài đặt cocoapods `gem install cocoapods`
    - nếu không cài đặt được thì bạn có thể sử dụng sudo để có đầy đủ quyền `sudo gem install cocoapods`
    - Nếu gặp lỗi về https://rubygems.org
        + cập nhật rvm để chắc chắn sử dụng version mới nhất của rvm: `rvm get stable`
        + cập nhật certificates: `rvm osx-ssl-certs update alll`
        + cập nhật ruby gems: `rvm ruby gems latest`
- mở thử mục project và sử dụng `pod setup --verbose`, verbose cho phép xem tiến độ cài đặt, không thì cứ ngồi chờ nó cài thôi mà hình bị forzen screen.
- tạo ra podfile, có thể dùng `pod init` hoặc `touch Podfile` cũng được
- mở file podfile: `open -a Xcode Podfile` và thêm các libs cần sử dụng.
- Run `pod install` để download và cài đặt. Đến đây là xong rồi đó
- Giờ thì chỉ dùng .xcworkspace để mở project, không dùng .xcodepoj nữa.
Now, We use .xcworkspase instead of .xcodeproj

###Cấu trúc file Podfile
{% highlight ruby %}
# Uncomment this line to define a global platform for your project
# platform :ios, '8.0'
use_frameworks!
 
target 'IceCreamShop' do
 
end
 
target 'IceCreamShopTests' do
 
end
{% endhighlight %}
ý nói là project đang target iOS 8.0 và sử dụng frameworks thay vì static libraries 
Để thêm các framework thì thêm trong target như : 
 
 - `pod 'Alamofire', '1.2.3'`
 - `pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git', :branch => 'swift-2.0'`

Node: Để dùng với swift thì **use_frameworks!** là bắt buộc

- Nếu có ai sử dụng từ **Swift library** thì thực tế ý họ là **Swift dynamic framework**, vì Swift không thể dùng static libraries. 
- iOS 8, apple giới thiệu về dynamic framework, cho phép code, images và các assets khác build chung với nhau. Trước iOS8, Cocoapods được tạo như static libraries (just fat binaries), và không cho phép chứa bất kỳ resource nào.
- dynamic framework có **namespace** classes, static libraries thì không. Với **namespace**, xcode có thể build proj với 2 classes có cùng tên nên khác framework.
- Không như objective-c, the standard Swift runtime không có trong iOS. Nghĩa là framework cần phải include Swift runtime libraries. Vậy nên nếu swift cho phép static libraries thì sẽ gặp lỗi duplicate symbols vì trùng standard runtime.


Tham khảo: [raywenderlich](http://www.raywenderlich.com/97014/use-cocoapods-with-swift)
