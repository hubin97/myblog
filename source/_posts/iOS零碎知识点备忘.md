---
title: iOS零碎知识点备忘
date: 2018-07-09 11:38:53
tags: [iOS,技术札记]
---

### 12/24小时制判断

```objc
#pragma mark -Private
/**
 hasAMPM==TURE为12小时制，否则为24小时制
 */
- (BOOL)hasAMPM
{
    //获取系统是24小时制或者12小时制
    NSString *formatStringForHours = [NSDateFormatter dateFormatFromTemplate:@"j" options:0 locale:[NSLocale currentLocale]];
    NSRange containsA = [formatStringForHours rangeOfString:@"a"];
    BOOL hasAMPM = containsA.location != NSNotFound;
    return hasAMPM;
}
```
<!--more-->

### 手机号码校验
2017年8月8日，工信部发布2017年第10批《电信网码号资源使用证书》颁发结果公示，涵盖130个新号段。
其中新批电信199号段、移动198号段、联通166号段。
`^1(3[0-9]|4[57]|5[0-35-9]|6[6]|7[06-8]|8[0-9]|9[89])\d{8}$`

```objc
- (BOOL)isMobileNO:(NSString *)phoneNumber {
    NSString *MOBILE = @"^1(3[0-9]|4[57]|5[0-35-9]|6[6]|7[06-8]|8[0-9]|9[89])\\d{8}$";
    NSPredicate *regextestmobile = [NSPredicate predicateWithFormat:@"SELF MATCHES %@", MOBILE];
    return [regextestmobile evaluateWithObject:phoneNumber];
}
```

### 沙箱操作

<!--iOS开发向沙盒中写入文件、文件夹以及从沙盒中读取文件
-->
<!--[document](http://blog.csdn.net/lizhilin_vip/article/details/53185482)
-->

1.获取document下面所有文件 (包括文件夹及文件夹里面的文件)

```objc
NSString *docPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0];
    NSString *imagesPath = [docPath stringByAppendingPathComponent:@"xxx"];

    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSDirectoryEnumerator *dirEnum = [fileManager enumeratorAtPath:imagesPath];
    
    NSString *fileName;
    while (fileName = [dirEnum nextObject])
    {
        NSLog(@"----------FielName : %@" , fileName);
        NSLog(@"-----------------FileFullPath : %@" , [imagesPath stringByAppendingPathComponent:fileName]) ;
    }

```

2.分别获取文件夹名, 文件夹里面的文件名

```objc
- (void)getUploadImageTask
{
    NSString *docPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0];
    NSString *imagesPath = [docPath stringByAppendingPathComponent:@"xxx"];

    NSFileManager *fileManager = [NSFileManager defaultManager];
    
    // 获取文件夹的名字数组
    NSArray *dirFileList = [[NSArray alloc] initWithArray:[fileManager contentsOfDirectoryAtPath:imagesPath error:nil]];
    DLog(@"dirFileList:%@",dirFileList);

    // 遍历文件夹取图片名数组
    for (NSString *dirName in dirFileList)
    {
        NSString *imageTotalPath = [imagesPath stringByAppendingPathComponent:dirName];
        
        NSArray *imageFileList = [[NSArray alloc] initWithArray:[fileManager contentsOfDirectoryAtPath:imageTotalPath error:nil]];
        DLog(@"imageFileList:%@",imageFileList);
        
        [_dataSource addObject: @{dirName : imageFileList}];
    }
}

```