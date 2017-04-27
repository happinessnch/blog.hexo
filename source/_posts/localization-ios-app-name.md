---
title: iOS应用名的本地化支持
date: 2016-04-16 19:44:37
tags: iOS
categories: iOS
---

## 环境
Xcode 7.2 & Mac OS X 10.11

## 配置Xcode
在TARGETS（App） -> Info中添加 **[Application has localized display name] [Boolean]** Key，并将其设置为 **YES**。 

![app_localization_xcode.png-95.9kB][1]
- - -
在PROJECT -> Info中添加所需要的本地化语言。

![localization-xcode-project-14-37.png-73.8kB][2]
- - -
## 创建本地化文本
在工程目录下，创建InfoPlist.strings文件，并为该文件设置本地化属性“Localization”。

![localization-xcode-create-strings-file-15-03.png-72.1kB][3]
- - -
点击**"Localize..."**，

![localization-InfoPlist-set-19-04.png-77.7kB][4]

设置需要的本地化语言。

![localization-set-plist-language-19-13.png-82.5kB][5]

设置不同语言下的App名称，

```C
    CFBundleDisplayName = "应用名称";
    CFBundleName = "App名字";
```


还可以通过NSHumanReadableCopyright Key设置版权声明的本地化配置，更多请参考：[官网链接 Localizing Your App](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html)

![localization-set-plist-appname.png-99.7kB][6]

- - - 
## 测试
完成后，可以通过设备上的**“语言与地区”**设置不同的语言进行测试。


  [1]: http://static.zybuluo.com/happinessnch/tjr6fsfzee478kbrx3bn05yl/app_localization_xcode.png
  [2]: http://static.zybuluo.com/happinessnch/utia3evuwusego4umr8xzjeq/localization-xcode-project-14-37.png
  [3]: http://static.zybuluo.com/happinessnch/3ja6ikggrjlvb7tesfgspjf0/localization-xcode-create-strings-file-15-03.png
  [4]: http://static.zybuluo.com/happinessnch/irdyb9w3gd1abjzt1477dbsr/localization-InfoPlist-set-19-04.png
  [5]: http://static.zybuluo.com/happinessnch/98wtyy8xdw80jpokpkrjdee6/localization-set-plist-language-19-13.png
  [6]: http://static.zybuluo.com/happinessnch/0jopaeokmg5ug1fu9szedt19/localization-set-plist-appname.png
