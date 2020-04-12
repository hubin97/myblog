---
title: 制作CocoaPods公有库
date: 2019-04-09 15:20:30
tags: [技术札记,iOS]
---
### 最终实现效果
执行`$pod search xxx`

```
-> HBKitTools (0.0.3)
   个人常用开发组件
   pod 'HBKitTools', '~> 0.0.3'
   - Homepage: https://github.com/hubin97/HBKitTools
   - Source:   https://github.com/hubin97/HBKitTools.git
   - Versions: 0.0.3, 0.0.2, 0.0.1 [trunk repo]
   - Subspecs:
     - HBKitTools/HBCategorys (0.0.3)
     - HBKitTools/HBUIViews (0.0.3)
```
<!--more-->

### 申请pod账号
使用`$pod trunk me`来查看自己的账号信息，如果没有账号的话需要先注册一下：

注册指令使用`$pod trunk register xxx@qq.com "<username>"`, 然后登录邮箱验证即可

邮箱验证完, 再次终端确认一下:

```
$pod trunk me
  - Name:     username
  - Email:    xxx@qq.com
  - Since:    March 29th, 21:16
  - Pods:
    - HBKitTools
  - Sessions:
    - March 29th, 21:16 - August 15th, 01:02. IP: xxx.xxx.xxx.xx
```

### 创建并配置podspec文件
创建指令`$pod spec create <filename>`; 

**这里提前说一下**,最好先使用`$pod search <filename>`查一下`filename`库名是否已有人创建, 避免发布的时候报错又得重新走一遍流程.

然后根据项目文件结构配置podspec文件, 如:

```
Pod::Spec.new do |spec|
  
  # ―――  Spec Metadata  ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  spec.name         = "HBKitTools"
  spec.version      = "0.0.3"
  spec.summary      = "个人常用开发组件"
  spec.description  = "个人常用开发组件-自定义视图,常用分类,技术总结类目等."
  spec.homepage     = "https://github.com/hubin97/HBKitTools"

  # ―――  Spec License  ――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  spec.license      = { :type => "MIT", :file => "LICENSE" }


  # ――― Author Metadata  ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  spec.author             = { "Hubin_Huang" => "970216474@qq.com" }

  # ――― Platform Specifics ――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  spec.platform     = :ios, "9.0"

  # ――― Source Location ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  spec.source       = { :git => "https://github.com/hubin97/HBKitTools.git", :tag => "#{spec.version}" }

  # ――― Source Code ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  spec.source_files  = "HBKitTools/HBKitTools/*.h"
  
  spec.subspec 'HBCategorys' do |dd|
    dd.source_files  = "HBKitTools/HBKitTools/HBCategorys/*"
  end
  
  spec.subspec 'HBUIViews' do |dd|
    dd.source_files  = "HBKitTools/HBKitTools/HBUIViews/*"
  end
  
  # ――― Project Settings ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  spec.requires_arc = true
  

end
```

关键部分文件结构如下:

```
├── HBKitTools
│   ├── HBCategorys
│   │   ├── HBCategorys.h
│   │   ├── UILabel+HBAlignment.h
│   │   ├── UILabel+HBAlignment.m
│   │   ├── UIView+HBExtension.h
│   │   └── UIView+HBExtension.m
│   ├── HBKitTools.h
│   └── HBUIViews
│       ├── HBTitleView.h
│       ├── HBTitleView.m
│       └── HBUIViews.h
```

一些配置字符解释, 可以参考 [podspec配置相关](https://www.jianshu.com/p/dd8eeaa3519f)

```
文件匹配
*匹配所有文件
c*匹配以名字C开头的文件
*c匹配以名字c结尾的文件
*c*匹配所有名字包含c的文件
**文件夹以及递归子文件夹
?任意一个字符(注意是一个字符)
[set] 匹配多个字符,支持取反
{p,q} 匹配名字包括p 或者 q的文件
```

### 验证podspec文件
在包含podspec文件夹下, 执行 `$pod lib lint ` 或者 `$pod lib lint xxx.podspec`

验证通过一般最后会有提示`xxx passed validation.`

这个时候的验证出现的问题, 主要是一些路径的配置错误, 如: *spec.source_files* 或者子路径,常见提示
`- ERROR | [iOS] file patterns: The source_files pattern did not match any file.`

### 发布podspec文件
先用git指令把代码提到github远端, 并打上标签版本号

```
$git add *
$git commit -m "提交描述信息"
$git tag '0.0.1'  #tag版本
$git push --tags
```
当然了,在打标签的时候可以使用指令`$git tag` 或者 `$git tag -l`去查看已有标签; 另外部分时候标签操作不够简明清晰, 建议使用**source tree**去操作.

发布执行 `$pod trunk push`或者 `$pod trunk push --allow-warnings`


### 查看发布的开源库
使用`$pod search <filename>`或者`$pod search <filename> --simple`立即查看? 但是实际上部署到cocoapods上去还需要一段时间生效, 另外你的本地也可能会存在缓存问题. 

这个时候通过 `https://cocoapods.org/pods/filename`去直接访问库的详细信息, filename即为开源库名称. **访问可能有时候会页面空白,请刷新重试**

<!--![](https://tva1.sinaimg.cn/large/00831rSTgy1gdnmyz5vaej30go08ct8s.jpg)
-->
### 常见问题

1.项目名称在cocoapod已经有人创建过了
```
[!] You (xxx@xx.com) are not allowed to push new versons for this pod.the owners of this pod are xxx@xx.com.
```
解决方案: 改名重头来过

2.本地的git tag跟podspec的版本不对应;
```
ERROR | [iOS] unknown: Encountered an unknown error (Pod::DSLError) during validation.
```

查看podspec配置和git tag列表

```
spec.version      = "0.0.2" #podspec文件配置
-- - --- --- 
$git tag -l
0.0.1
0.0.2
0.0.3
$git tag  #修改完再查看
0.0.1
0.0.2
```
解决方案: 使用`source tree`对标签进行修正 (命令行操作远端可参考: [Git中tag的用法及作用](https://www.cnblogs.com/fanheyan/articles/9753363.html))

3.提示无远端分支

```
- ERROR | [iOS] unknown: Encountered an unknown error ([!] /usr/bin/git clone ... —depth 1 —branch 0.0.1 ... 
fatal: Remote branch 0.0.1 not found in upstream origin
) during validation.

[!] The spec did not pass validation, due to 1 error.
```
解决方案: 检查本地代码和标签并推到远端

### 相关参考

pod trunk 几个常用指令 [参考细说 pod trunk](https://www.dazhuanlan.com/2020/02/02/5e36624336d55/)


