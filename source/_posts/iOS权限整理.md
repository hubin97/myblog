---
title: iOS权限整理
date: 2018-07-10 18:03:08
tags: [iOS,技术札记]
---

相机/相册/麦克风权限判断

```objc
// 判断相机是否可以打开
if (![UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypeCamera]) return;

// 判断相册是否可以打开
if (![UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypePhotoLibrary]) return;

// 麦克风权限
//FIXME:获取麦克风权限(避免iOS10接听时提示权限闪退)
[[AVAudioSession sharedInstance] requestRecordPermission:^(BOOL granted) {
    if (granted) {
       NSLog(@"用户同意获取麦克风!!!");
       } else {
       NSLog(@"用户不同意获取麦克风!!!");
       }
 }];
```
<!--more-->

plist文件配置如下

```XML
<!-- 相册 --> 
<key>NSPhotoLibraryUsageDescription</key> 
<string>App需要您的同意,才能访问相册</string> 
<!-- 相机 --> 
<key>NSCameraUsageDescription</key> 
<string>App需要您的同意,才能访问相机</string> 
 
<!-- iOS11 新增 --> 
<key>NSPhotoLibraryAddUsageDescription</key>
 <string>App需要您的同意,才能添加照片到相册</string>

<!-- 麦克风 --> 
<key>NSMicrophoneUsageDescription</key> 
<string>App需要您的同意,才能访问麦克风</string> 
<!-- 位置 --> 
<key>NSLocationUsageDescription</key> 
<string>App需要您的同意,才能访问位置</string> 
<!-- 在使用期间访问位置 --> 
<key>NSLocationWhenInUseUsageDescription</key> 
<string>App需要您的同意,才能在使用期间访问位置</string> 
<!-- 始终访问位置 --> 
<key>NSLocationAlwaysUsageDescription</key> 
<string>App需要您的同意,才能始终访问位置</string> 
<!-- 日历 --> 
<key>NSCalendarsUsageDescription</key> 
<string>App需要您的同意,才能访问日历</string> 
<!-- 提醒事项 --> 
<key>NSRemindersUsageDescription</key> 
<string>App需要您的同意,才能访问提醒事项</string> 
<!-- 运动与健身 --> 
<key>NSMotionUsageDescription</key> <string>App需要您的同意,才能访问运动与健身</string> 
<!-- 健康更新 --> 
<key>NSHealthUpdateUsageDescription</key> 
<string>App需要您的同意,才能访问健康更新 </string> 
<!-- 健康分享 --> 
<key>NSHealthShareUsageDescription</key> 
<string>App需要您的同意,才能访问健康分享</string> 
<!-- 蓝牙 --> 
<key>NSBluetoothPeripheralUsageDescription</key> 
<string>App需要您的同意,才能访问蓝牙</string> 
<!-- 媒体资料库 --> 
<key>NSAppleMusicUsageDescription</key> 
<string>App需要您的同意,才能访问媒体资料库</string>

<key>UIBackgroundModes</key>
<array> 
<!-- 在这里写上你在后台模式下要使用权限对应的key --> 
<string>location</string>
...
</array>
```

部分英语提示参考

```XML
<key>NSLocationUsageDescription</key>
	<string>App need your approval to access location</string>
<key>NSMicrophoneUsageDescription</key>
	<string>App need your approval to access microphone</string>
<key>NSPhotoLibraryUsageDescription</key>
	<string>App need your approval to access photo album</string>
<key>NSCameraUsageDescription</key> 
	<string>App need your approval to access camera</string> 
```
