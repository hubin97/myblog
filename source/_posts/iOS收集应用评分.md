---
title: iOS收集应用评分
date: 2019-04-24 10:14:23
tags: [iOS,技术札记]
---

### 需求背景
还在使用弹框链接跳转收集评分? 太不人性话了,应用用的好好的突然打断,比较闹心.

###  使用细节

*SKStoreReviewController* 注意: iOS 10.3+ 官方才提供的内置评分系统, 不能做成事件触发弹框评分的方式, 即使测试环境正常, 上架后事件触发仍会表示不响应. 其他细节如下:

<!--more-->

1. 即使调用 requestReview, 也不一定会出现UI
2. 测试环境不能点提交按钮
3. 只能评星, 不能提交评论内容
4. 提示次数不限制, 但不要过于频繁, 官方建议每个版本提示一次
5. 旧版本收集如果写评论内容:可以在openURL后面拼接 action=write-review, 可以直接跳转到App Store评论页
6. 参考 [官方文档](https://developer.apple.com/documentation/storekit/skstorereviewcontroller/requesting_app_store_reviews?language=objc)

### Simple code

```objc
#import <StoreKit/StoreKit.h> 

/**
 收集评分
 */
- (void)showAppStoreReView {
    NSDictionary *infoDictionary = [[NSBundle mainBundle] infoDictionary];
    NSString *app_Version = [infoDictionary objectForKey:@"CFBundleShortVersionString"];
    NSString *app_build = [infoDictionary objectForKey:@"CFBundleVersion"];
    _appVersion = [NSString stringWithFormat:@"%@ (build %@)",app_Version,app_build];
    
    // 是否已经收集评分
    NSNumber *isShowAppStoreReView = [[[NSUserDefaults standardUserDefaults] objectForKey:_appVersion] objectForKey:@"review"];
    if (isShowAppStoreReView && [isShowAppStoreReView isEqualToNumber:@YES]) return;
    
    // 展示的时刻
    int shortestTime = 50;
    int longestTime = 500;
    int timeInterval = arc4random_uniform(longestTime - shortestTime) + shortestTime;
    
    // 测试
    //timeInterval = 10;
    [NSTimer scheduledTimerWithTimeInterval:timeInterval target:self selector:@selector(requestReview) userInfo:nil repeats:NO];
}

- (void)requestReview
{
    NSMutableDictionary *reviewDict = [NSMutableDictionary dictionaryWithDictionary:[[NSUserDefaults standardUserDefaults] objectForKey:_appVersion]];
    [reviewDict setObject:@YES forKey:@"review"];
    [[NSUserDefaults standardUserDefaults] setObject:reviewDict forKey:_appVersion];
    [[NSUserDefaults standardUserDefaults] synchronize];

    if (@available(iOS 10.3, *)) {
        [[UIApplication sharedApplication].keyWindow endEditing:YES];
        [SKStoreReviewController requestReview];
    }
    // 传统弹框提示跳转(iOS 10.3以下的)太麻烦直接取消提示
    
//    else {
//        //不论iOS版本均可使用APP内部打开网页形式，跳转到App Store 直接编辑评论
//        UIAlertController * alert = [UIAlertController alertControllerWithTitle:@"温馨提示" message:@"给App打个分吧?" preferredStyle:UIAlertControllerStyleAlert];
//        UIAlertAction * ensure = [UIAlertAction actionWithTitle:@"去评论" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
//
//            dispatch_async(dispatch_get_main_queue(), ^{
//
//                NSString *APPID = @"xxx";
//                NSString *nsStringToOpen = [NSString stringWithFormat: @"itms-apps://itunes.apple.com/app/id%@?action=write-review",APPID];
//                [[UIApplication sharedApplication] openURL:[NSURL URLWithString:nsStringToOpen]];
//            });
//        }];
//        UIAlertAction * cancel = [UIAlertAction actionWithTitle:@"下次吧" style:UIAlertActionStyleDefault handler:nil];
//        [alert addAction:cancel];
//        [alert addAction:ensure];
//
//        [self.window.rootViewController presentViewController:alert animated:YES completion:nil];
//    }
}
```
