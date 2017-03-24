---
layout: post
title: Github代码添加Cocoapods支持
date: 2017-03-24 16:52:0.000000000 +09:00
tags: iOS,Cocoapods
---

### [简书文章地址](http://www.jianshu.com/p/b9a28b6ab1e1)

### 需求
作为一名iOS应用开发者，在实际工作中会经常使用到第三方开源类库,比如JSONKit、AFNetWorking、SDWebimage等等。这些第三放开源类库添加Cocoapods支持，为我们实际开发工作中提供不少的便利。当慢慢开始习惯Cocoapods给我们带来的便利时，就开始想让自己的常用的工具类也添加Cocoapods支持，既然有了需求那么，就开始实现这个过程了，走过路过，千万不要错过，下面开始干货分享。

附上本文实现的demo：[MD5(32位/64位)加密](https://github.com/hnxyzhw/ZHWTools)

---

### 解决需求

#### 流程步骤

* 注册trunk
* 在Github上传相关的代码库
* 创建podspec配置文件
* 验证是否可用
* trunk push 代码到 CocoaPods
* 上传成功后检查是否可用

----

#### 注册trunk

想让Cocoapods支持你自己的工具代码库，需要一个CocoaPods账号。

注册命令：

```
pod trunk register 邮箱地址 ‘用户名’ —description='描述信息'
```

执行以上注册命令后，到你注册邮箱检查一下，会有相关的邮件，点击验证链接打开网页。如果出现下面的提示，说明注册成功(如果打开网页太慢的话，建议开vpn)。

![Snip20170308_6.png](http://upload-images.jianshu.io/upload_images/683658-6723883136b4936e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

查看注册信息命令：

```
pod trunk me
```

执行该命令会出现以下输出：

![Snip20170308_7.png](http://upload-images.jianshu.io/upload_images/683658-b24c45fa712eec29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

至此，我们就注册完CocoaPods账号，可以trunk push我们的自己的代码库。

---

#### Github上传代码库

需要到[github官网](https://github.com)登录你的账户，执行Create a new repository操作。如下图所示：

![Snip20170307_1.png](http://upload-images.jianshu.io/upload_images/683658-44e142257a88b855.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

注意MIT License选项，这个会在添加Cocoapods支持的时候用到。创建完成后，可以通过GitHub Desktop软件clone到本地。目录结构如下图：

![Snip20170307_3.png](http://upload-images.jianshu.io/upload_images/683658-fdc6448a0f9384ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

可以看到这里的会有一个LICENSE的文件，就是刚刚选着MIT License所创建的。ZHWTools是示例工程，我们要开源的工具类代码库，在示例工程的子文件夹下。

---

#### 创建podspec配置文件

打开终端，在cd到刚刚的文件夹目录下，执行创建podspec命令：

```
//ZHWtools是你要开源的库名
pod spec create ZHWtools
```
执行后文件夹里会出现相关的podspec文件，下面需要编辑这个文件，可以在终端执行`vim ZHWtools.podspec`进行编辑。也可以用其他的文本编辑器打开，进行编辑。如下图所示：

![Snip20170307_4.png](http://upload-images.jianshu.io/upload_images/683658-4b8c342899c9aa06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

简单介绍下相关代码行的意义：

```
s.name：名称，pod search 搜索的关键词,注意这里一定要和.podspec的名称一样,否则报错
s.version：版本号
s.ios.deployment_target:支持的pod最低版本
s.summary: 简介
s.homepage:项目主页地址
s.license:许可证
s.author:作者
s.social_media_url:社交网址,这里我写的微博默认是Twitter,如果你写Twitter的话,你的podspec发布成功后会@你
s.source:项目的地址
s.source_files:需要包含的源文件
s.resources: 资源文件
s.requires_arc: 是否支持ARC
s.dependency：依赖库，不能依赖未发布的库
s.dependency：依赖库，如有多个可以这样写
```
如果有什么不清楚可以去看一下那么比较知名的开源库（AFNetworking等），参考一下。

需要注意的source_files这个路径的填写，这里也简单说明一下。

```
"*" 表示匹配所有文件
"*.{h,m}" 表示匹配所有以.h和.m结尾的文件
"**" 表示匹配所有子目录
```

s.source 常见写法

```
commit => "68defea" 表示将这个Pod版本与Git仓库中某个commit绑定
tag => 1.0.0 表示将这个Pod版本与Git仓库中某个版本的comit绑定
tag => s.version 表示将这个Pod版本与Git仓库中相同版本的comit绑定
```

---

#### 验证是否可用

执行以下命令进行验证：

```
pod lib lint ZHWtools.podspec
```
如果有没有Erro输出，只是WARN输出，那么可以将这些警告直接忽略,忽略警告命令。

```
pod lib lint ZHWtools.podspec —allow-warnings
```

![Snip20170308_5.png](http://upload-images.jianshu.io/upload_images/683658-43cfefd961cc48ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当终端输出`ZHWtools passed validation`之后，就说明验证通过了。

在执行校验命令是会出现下面的类似错误情况，简单说明一下，供各位看官参考。

返回错误结果：

```
  - ERROR | xcodebuild: Returned an unsuccessful exit code. You can use `--verbose` for more information.
    - NOTE  | [OSX] xcodebuild:  /Users/wangguimin/UpTextField/UpTextField/UIView+Extension.h:8:9: fatal error: 'UIKit/UIKit.h' file not found
    - NOTE  | [OSX] xcodebuild:  /Users/wangguimin/UpTextField/UpTextField/UIColor+ColorWithHexStrig.h:9:9: fatal error: 'UIKit/UIKit.h' file not found
```
解决办法1：
在podspec配置文件里，修改 s.frameworks s.platform选项（s.platform这个指的是兼容iOS 8.0以后的版本）。

```
s.frameworks   = "Foundation"
s.platform     = :ios, "8.0"
```
解决办法2:
在你的示例工程里，创建一个pch文件，讲说有的要开源的代码库的`.h`头文件导入到pch文件里。
然后在执行`pod lib lint ZHWtools.podspec —allow-warnings`命令。

所有错误都解决后，我们需要这些修改都重新推到GitHub上(库文件 和 .spec文件)，可以使用GitHub Desktop直接提交。也可使用以下命令来提交并打上标记:

```
git tag '1.0.0'git push --tags
```
---

#### trunk push 代码到 CocoaPods

所有的准备工作都已经完成了，下面就需要将我们编写的文件push到Cocoapods。

执行以下命令：

```
pod trunk push ZHWtools.podspec
```
如下图所示成功结果:

![Snip20170308_10.png](http://upload-images.jianshu.io/upload_images/683658-affe15d528b90258.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果出现下面的错误提示，可以试着在网络的DNS选项添加一个`114.114.114.114`的DNS服务器选项(建议开VPN)：

![Snip20170308_11.png](http://upload-images.jianshu.io/upload_images/683658-fcec678f2f6f0cf8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

---

#### 上传成功后检查是否可用

检验是否上传成功命令:

```
pod search ZHWtools
```

如果搜索不到，建议删除本地的CocoaPods的搜索目录，执行以下命令：

```
rm ~/Library/Caches/CocoaPods/search_index.json
```

![Snip20170308_12.png](http://upload-images.jianshu.io/upload_images/683658-8b6d45e50522b4d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再重新执行`pod search ZHWtools`命令搜索就可以看到，我们的开源的工具代码库的相关信息，可以通过pod 命令安装到工程中使用。
ps:如果你发布成功后，在你自己的电脑上可以search到，在别的电脑上搜不到，建议执行`rm ~/Library/Caches/CocoaPods/search_index.json`命令，先删除旧的搜索目录。也可以尝试更新你的pod。


