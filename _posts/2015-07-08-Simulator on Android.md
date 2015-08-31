---
layout: post
title: How to detect Android Simulator
categories: [Development]
tags: [Android]
description: Vấn đề về việc detect simulator, có thể sử dụng đoạn code sau, mặc dùng không hoàn toàn đúng trong tất cả các trường hợp (thêm đk để chính xác hơn, mà simulator nhiều quá chưa thêm hết được)
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
        
        String fingerPrint = Build.FINGERPRINT;
        if (result == false && fingerPrint != null) {
            Log.d("G6Pig", "__Fingerprint: " + fingerPrint);
            result = fingerPrint.contains("generic") || fingerPrint.contains("unknown");
        }
        
        String model = Build.MODEL;
        if (result == false && model != null) {
            Log.d("G6Pig", "__Model: " + model);
            result = model.contains("google_sdk") ||
                    model.contains("Android SDK built for x86") ||
                    model.contains("VirtualBox") ||
                    model.contains("Droid4X");
        }
        
        String manufacturer = Build.MANUFACTURER;
        if (result == false && manufacturer != null) {
            Log.d("G6Pig", "__Manufacturer: " + manufacturer);
            result = manufacturer.contains("Genymotion");
        }
        
        String brand = Build.BRAND;
        String device = Build.DEVICE;
        if (result == false && brand != null && device != null) {
            Log.d("G6Pig", "__Brand: " + brand + " __Device: " + device);
            result = (brand.contains("generic") && device.contains("generic"));
        }
        
        String product = Build.PRODUCT;
        if (result == false && product != null) {
            Log.d("G6Pig", "__Product: " + product);
            result = product.matches(".*_?sdk_?.*") || product.contains("Droid4X");
        }
        
        if (G6PigUtils.m_activity != null) {
            try {
                boolean isSupportEs2 = isSupportGLES20((ActivityManager)G6PigUtils.m_activity.getSystemService(Context.ACTIVITY_SERVICE));
                if (isSupportEs2) {
                    Log.d("G6Pig", "__isSupportEs2: " + isSupportEs2);
                    String vendor = GLES20.glGetString(GLES20.GL_VENDOR);
                    if (result == false && vendor != null) {
                        Log.d("G6Pig", "__Vendor: " + vendor);
                        Log.d("G6Pig", "__Renderer: " + GLES20.glGetString(GLES20.GL_RENDERER));
                        Log.d("G6Pig", "__Hardware: " + Build.HARDWARE);
                        result = vendor.contains("BlueStacks");
                    }   
                } else {
                    Log.d("G6Pig", "__isSupport Es10");
                    String vendor = GLES10.glGetString(GLES10.GL_VENDOR);
                    if (result == false && vendor != null) {
                        Log.d("G6Pig", "__Vendor: " + vendor);
                        Log.d("G6Pig", "__Renderer: " + GLES10.glGetString(GLES10.GL_RENDERER));
                        Log.d("G6Pig", "__Hardware: " + Build.HARDWARE);
                        result = vendor.contains("BlueStacks");
                    }
                }   
            } catch (Exception e) {
                // TODO: handle exception
                e.printStackTrace();
            }
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
