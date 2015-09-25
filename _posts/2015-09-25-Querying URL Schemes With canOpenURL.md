---
layout: post
title: Querying URL Schemes With canOpenURL
categories: [transtate]
tags: [iOS 9]
description: Now for a less flippant response, with some suggestions. Don't take these as implementation recommendations, more as examples of possible use.
---

Apple tiếp tục đặt ưu tiên cao trong việc bảo vệ sự riêng tư của người dùng nên cũng không ngạc nhiên rằng iOS 9 mang cách bảo mật và cách đo lượng sự riêng tư mới. Một trong các cách đo lường là ngăn chặn việc lạm dụng canOpenURL để thu thập các ứng dụng mà user đã cài đặt.

###Querying a URL Scheme

Từ lâu, ta có thể mới một ứng dụng thứ 3 bằng cách sử dụng phương thức openURL:. Điển hình như đầu tiên bạn truy vấn cho URL scheme bằng các canOpenURL: sau đó mở nó với openURL:. Trong objective-c có thể sử dụng như sau:

{% highlight objective-c %}
// Expects the URL of the scheme e.g. "fb://"
- (BOOL)schemeAvailable:(NSString *)scheme {
  UIApplication *application = [UIApplication sharedApplication];
  NSURL *URL = [NSURL URLWithString:scheme];
  return [application canOpenURL:URL];
}
{% endhighlight %}

Hoặc nếu bạn thích swift, có thể nó sẽ giống như sau:

{% highlight swift %}
func schemeAvailable(scheme: String) -> Bool {
    if let url = NSURL.init(string: scheme) {
        return UIApplication.sharedApplication().canOpenURL(url)
    }
    return false
}
{% endhighlight %}

Để truy vấn đến Facebook và Twitter apps:

{% highlight swift %}
// Objective-C
BOOL fbInstalled = [self schemeAvailable:@"fb://"];
BOOL twInstalled = [self schemeAvailable:@"twitter://"];

// Swift
let fbInstalled = schemeAvailable("fb://")
let twInstalled = schemeAvailable("twitter://")
{% endhighlight %}

Điều này hửu dụng nhưng các lập trình viên bao gồm Twitter và facebook đang dùng cách thức này để thu thập danh sách các apps được cài trên device từ đó họ có thể phân phối các nội dung phù hợp (tailored content). Apple đã cân nhắc điều này là một sự vi phạm quyền riêng tư và trong iOS 9 đã giới hạn cách truy vấn of URL Schemes. *nếu bạn build và tương thích ngược đến iOS 9 SDK (link against), bạn cần liệt kê các schemes mà app của bạn sẽ query*. Điều gì quan trong để hiểu rằng chính sách này có thể cũng ảnh hưởng đến những apps cũ, những apps chưa được build lại với iOS 9 SDK. Hãy xem 2 bối cảnh sau:

###An iOS 8 App Running on iOS 9

Để dựng trường hợp app iOS 8 chạy trên device iOS 9, tôi xây dựng 1 app để test với Xcode 6 dùng iOS 8.4 SDK. Sau đó tôi chạy nó trên device iOS 9 với duy nhất Twitter app được cài sẵn. Tôi kỳ vọng query cho facebook scheme sẽ trả về NO và twitter scheme là YES. Nó cũng có 1 thông báo cảnh báo trên debug log về lỗi query:

{% highlight swift %}
CanOpen[2255:1002610] -canOpenURL: failed for URL:
 "fb://" - error: "(null)"
{% endhighlight %}

Điều này đúng, nghĩa là phần lớn các apps có đáp trả khi query và opening scheme sẽ tiếp tục hoạt động, nó không bị thay đổi trên iOS 9. Tôi nói phần lớn vì ở đây có 1 giớ hạn quan trọng cho các app không liên kết ngược iOS 9. Khi bạn chạy 1 app trên iOS 9 ở đó có 1 giớ hạn về 50 scheme khác nhau.

*Nếu bạn thực thi đến giới hạn (50 distinct schemes) canOpenURL sẽ trả về NO. Biến đếm distinct schemes trong app của bạn không bị reset kể cả khi restart device.*

Để kiểm chứng điều này, tôi đã query 100 schemes khác nhau để xem điều gì sẽ diễn ra:

<% highlight swift %>
for (int scheme = 0; scheme < 100; scheme++) {
    [self schemeAvailable:[NSString stringWithFormat:@"%02d://",scheme]];
}
<% endhighlight %>

các queries đầu, kết quả giống như được dự đoán, scheme không mở được vì không có app tương ứng.
<% highlight swift %>
-canOpenURL: failed for URL: "00://" - error: "(null)"
-canOpenURL: failed for URL: "01://" - error: "(null)"
<% endhighlight %>

Khi thực thi được 50 schemes khác nhau (gồm cả facebook và twitter đã gọi từ trước đó), thông báo lỗi đã thay đổi
<% highlight swift %>
-canOpenURL: failed for URL: "48://" - error:
 "This app has exceeded the number of allowed scheme queries"
<% endhighlight %>

Kể từ điềm này, nó không thể query các schemes mới. Bạn có thể vãn query facebook, twitter vì nó thuộc 50 schemes được cho phép nhưng nếu bạn cần query một scheme khác thì không được

###App Linked with iOS 9

Chuyển sang Xcode 7 để build và link bài test. Query cho facebook và twitter, giờ đã lỗi permission error:

{% highlight swift %}
-canOpenURL: failed for URL: "fb://" - error:
 "This app is not allowed to query for scheme fb"
-canOpenURL: failed for URL: "twitter://" - error:
 "This app is not allowed to query for scheme twitter"
{% endhighlight %}

Trong iOS 9, bạn phải liệt kê bất cứ URL schemes mà app của bạn muốn sử dụng trong info.plist đưới LSApplicationQueriesSchemes key.



Với các scheme được nêu trong info.plist mọi thứ sẽ làm việc như trước. *Khi bạn tương thich ngược iOS 9 bạn sẽ không bị giới hạn 50 distinct schemes* bạn chỉ cần chỉ ra url scheme trong info.plist. Cách đó dường như không giới hạn bao nhiêu schemes bạn có thể liệt kê nhưng tôi đang đợi câu hỏi từ App Store review team nếu họ nghĩ bạn đang lạm dụng cơ chế.

Chú ý rằng cơ chế này chỉ áp dụng canOpenURL chứ không phải cho openURL. Bạn không cần phải liệt kê scheme trong info.plist để có thể mở với openURL.

Dịch từ: [querying-url-schemes-with-canopenurl](http://useyourloaf.com/blog/querying-url-schemes-with-canopenurl.html) dịch hơi sát, bà con thông cảm =.=
Tham khảo: [stackexchange](http://gamedev.stackexchange.com/a/4161)