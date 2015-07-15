---
layout: post
title: How to use CocoaPods with Swift
categories: [Development]
tags: [Swift]
description: CocoaPods dùng để quản lý các thành phần liên quan (cụ thể là lib, framework) cho project cocoa.
---

Để build sản phẩn android thường ta hay build trên simulator test trước khi deliver để test trên real device.
Một số các simulator thường hay dùng:
- default simulator trên eclipse (cái này thì chậm thôi rồi)
- genymotion 
- bluestack 
- droid4x 

Đối với bluestack, bình thường sẽ không tự connect với eclipce để debug. Muốn debug trên bluestack thì:
- mở cmd 
- vào thư mục android sdk (~/Library/Android/)
- vào thư mục platform-tools 
- dùng lệnh `adb connect localhost` hoặc `adb connect localhost:5555`
Note: không hiếu sao sau khi connect xong rồi thì default simulator của ecipse không in log ra được nữa

Vấn đề về việc detect simulator, có thể sử dụng đoạn code sau, mặc dùng không hoàn toàn đúng trong tất cả các trường hợp (thêm đk để chính xác hơn, mà simulator nhiều quá chưa thêm hết được)
{% highlight java %}
public static boolean IsSimulator() {
        boolean result = false;
        Log.d("G6Pig", "Fingerprint: " + Build.FINGERPRINT + " Model: " + Build.MODEL + 
                " Manufacturer: " + Build.MANUFACTURER);
        result = 
                Build.FINGERPRINT.contains("generic") ||
                Build.FINGERPRINT.contains("unknown") ||
                Build.MODEL.contains("google_sdk") ||
                Build.MODEL.contains("Android SDK built for x86") ||
                Build.MODEL.contains("VirtualBox") ||
                Build.MODEL.contains("Droid4X") ||
                Build.MANUFACTURER.contains("Genymotion");
        
        if (result == false) {
            Log.d("G6Pig", "Brand: " + Build.BRAND + " Device: " + Build.DEVICE);
            result |= Build.BRAND.contains("generic") && Build.DEVICE.contains("generic");
        }
        
        if (result == false) {
            Log.d("G6Pig", "Product: " + Build.PRODUCT);
            result |= Build.PRODUCT.matches(".*_?sdk_?.*") || Build.PRODUCT.contains("Droid4X");
        }
        
        return result;
    }
{% endhighlight %}

Log trên các simulator:

 - Eclipce simulator
 
Fingerprint: generic_x86/sdk_google_phone_x86/generic_x86:5.0.1/LSX66B/1630668:eng/test-keys 
Model: Android SDK built for x86 
Manufacturer: unknown

 - Genymotion
 
Fingerprint: generic/vbox86p/vbox86p:4.3/JLS36G/eng.buildbot.20150609.213200:userdebug/test-keys
Model: Google Galaxy Nexus - 4.3 - API 18 - 720x1280 
Manufacturer: Genymotion

 - BlueStack 
 
Fingerprint: samsung/kltexx/klte:4.4.2/KOT49H/G900FXXU1ANCE:user/release-keys
Model: VirtualBox 
Manufacturer: innotek GmbH

 - Droid4X
 
Fingerprint: Android/vbox86p/vbox86p:4.2.2/JDQ39E/eng.work.20150305.173317:eng/test-keys 
Model: Droid4X-MAC 
Manufacturer: MIT

Tham khảo: [android-emulator-detection](http://versprite.com/og/android-emulator-detection/)
