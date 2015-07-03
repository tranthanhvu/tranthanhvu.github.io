---
layout: post
title: App Thinning
categories: [Development]
tags: [iOS 9]
description: Khi ra mắt iOS9 Apple cũng đưa cách optimization vào, được gọi là app thinning. Nó giúp cho ứng dụng có thể sử dụng được trên nhiều device nhất, tốn tin dung lượng lưu trữ hơn và khi Apple có thay đổi về phần cứng, developer cũng không cần phải update application của họ để tương thích, Apple có thể tự làm điều này.
---

Khi ra mắt iOS9 Apple cũng đưa cách optimization vào, được gọi là app thinning. Nó giúp cho ứng dụng có thể sử dụng được trên nhiều device nhất, tốn tin dung lượng lưu trữ hơn và khi Apple có thay đổi về phần cứng, developer cũng không cần phải update application của họ để tương thích, Apple có thể tự làm điều này. 
**App thinning có thể được chia làm 3 phần: slicing, bitcode, on-demand resource**

###Slicing (iOS)
tạo ra nhiều biến thể để phù hợp với các loại device khác nhau. Trước đây khi build ứng dụng, các architectures của nhiều loại device đều được đóng gói trong cùng 1 build, gây ra file ipa có dung lượng lớn (arm64, arvm7s, armv7 ...). Với Slicing, developer vẫn sẽ gởi bản archive với full version, phần việc còn lại là của apple. Khi người dùng install application, bản phù hợp với device của người dùng sẽ được install.
Trong quá trình develop application, các build phù hợp với device cũng sẽ được xcode install lên device.
Note: đối với iOS, sliced app chỉ support cho các device iOS 9.0 trở lên, các trường hợp còn lại vẫn sẽ install full version.

###Bitcode (iOS, watchOS)
Được xem là thành phần trung gian đại diện cho compiled program. Nó cho phép apple có thể optimize app binary trong tương lai (khi apple có thay đổi về phần cứng, ra mắt thiết bị mới chẳng hạn), developer không phải submit lại app.
Note: đối với iOS, bitcode được để default nhưng không bắt buộc. Nếu dùng thì tất cả các framework trong app đều cần include bitcode. Đối với watchOS apps, bitcode là bắt buộc.

###On-Demand Resource (iOS)
Các resource như images, sounds có thể được tag bằng keyworks và được dùng trong groups. Apple sẽ host resource trên apple server và quản lý download. Nó sẽ giúp app ít dung lượng hơn, download nhanh hơn, có cả imporving the first-time lauch experience nữa nhưng mình không hiểu lắm, vì theo mình hiểu thì lần đầu lauch vào sẽ bắt download resource. Suy ra đối với những user mà họ download về để sau chơi, nhưng muốn chơi không có wifi, 3G cũng là 1 cái bất tiện. Dù sao thì nó cũng phụ thuộc vào developer có muốn On-Demand Resource ko. Apple cũng ví dụ về trường hợp On-Demand Resource cho các game chia nhiều level, theo như họ bảo là nó sẽ work transparently in the backgrond, hi vọng sẽ ngon (Nếu có thời gian mình sẽ viết thử xem :D). Đối với các app có tính năng này, khi distribution ngoài môi trường appstore, dĩ nhiên cần phải tự host resource ở đâu đó rồi. 

###Nhược điểm: 
 - Đối với bitcode, nhiều developers đang lo ngại việc reproduce vì nếu phát sinh bug do apple can thiệp vào thì họ khônng reproduce được.
 
###Ưu điểm: 
 - download nhanh hơn
 - dung lượng app nhỏ 
 - không cần re-submit nếu không có thay 
 - resource được quản lý bởi apple, không cần phải tạo host để quản lý ở ngoài (thực ra vẫn chưa biết lợi hay hại, trước mắc thì có là tốt rồi :D)

Tham khảo: [AppThinning](https://developer.apple.com/library/prerelease/watchos/documentation/IDEs/Conceptual/AppDistributionGuide/AppThinning/AppThinning.html")
