---
layout: post
title: iOS中NSString的strong、copy的使用
date: 2016-08-30 16:15:34.000000000 +09:00
---
&#160; &#160; &#160; &#160;iOS开发中关于内存的管理有两种，一种是基于ARC(Automatic Reference Counting)环境下的，另一种是MRC(Mannul Reference Counting)。这两种模式可以在工程中的Build Settings选项下设置，可参照下图所示：
****
![ARC/MRC.png](http://upload-images.jianshu.io/upload_images/683658-f796e399a5cc4ca9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
       说明：设置为Yes是ARC环境下，设置为NO是MRC环境下。
****
&#160; &#160; &#160; &#160;进入正题，我们在声明一个NSString类型的属性时，会遇到这样的一个问题。就是应该使用strong呢？还是应该用copy呢？下面我们通过具体的代码来分析一下两者的区别跟用法。

**操作：**
&#160; &#160; &#160; &#160;首先我们先声明两个不同的字符串属性，用来做比对，代码如下：

```
@interface ViewController ()
@property(nonatomic,strong) NSString *theStrongStr; //strong 字符串
@property(nonatomic,copy) NSString *theCopyStr;     //copy 字符串
@end
```
&#160; &#160; &#160; &#160;theStrongStr的内存特性是strong，theCopyStr的内存特性是copy，以便我们区分。
****

&#160; &#160; &#160; &#160;创建下面两个方法testString，testMutabelString：

```
-(void)testString{
    NSLog(@"-------------NString--------------");
    //创建一个不可变源字符串
    NSString *originStr = @"iOS";
    //初始化strong字符串
    self.theStrongStr = originStr;
    //初始化copy字符串
    self.theCopyStr = originStr;
    
    //打印字符串指向的地址,已经对应的内存地址
    NSLog(@"the origin string:%p, %p",originStr,&originStr);
    
    NSLog(@"the strong string:%p, %p",_theStrongStr,&_theStrongStr);
    
    NSLog(@"the copy string:%p, %p",_theCopyStr,&_theCopyStr);
    
}
```
****

```
-(void)testMutabelString{
    NSLog(@"-------------NSMutableString--------------");
    //创建一个可变源字符串
    NSMutableString *originStr = [NSMutableString stringWithFormat:@"iOS"];
    //初始化strong字符串
    self.theStrongStr = originStr;
    //初始化copy字符串
    self.theCopyStr = originStr;
    
    //打印字符串指向的地址,已经对应的内存地址
    NSLog(@"the origin string:%p, %p",originStr,&originStr);
    
    NSLog(@"the strong string:%p, %p",_theStrongStr,&_theStrongStr);
    
    NSLog(@"the copy string:%p, %p",_theCopyStr,&_theCopyStr);
}
```
&#160; &#160; &#160; &#160;在viewDidLoad里调用这个两个方法：

```
- (void)viewDidLoad {
    [super viewDidLoad];
    [self testString];
    [self testMutabelString];
}
```
&#160; &#160; &#160; &#160;运行看到输出的结果如下：

```
iOSCopyAndStrong[1076:1312388] -------------NString--------------
iOSCopyAndStrong[1076:1312388] the origin string:0x109eff098, 0x7fff55d00958
iOSCopyAndStrong[1076:1312388] the strong string:0x109eff098, 0x7fb0a2c55050
iOSCopyAndStrong[1076:1312388] the copy string:0x109eff098, 0x7fb0a2c55058
iOSCopyAndStrong[1076:1312388] -------------NSMutableString--------------
iOSCopyAndStrong[1076:1312388] the origin string:0x7fb0a2f79d50, 0x7fff55d00958
iOSCopyAndStrong[1076:1312388] the strong string:0x7fb0a2f79d50, 0x7fb0a2c55050
iOSCopyAndStrong[1076:1312388] the copy string:0xa00000000534f693, 0x7fb0a2c55058

```
****

**testString：**
&#160; &#160; &#160; &#160;在testString方法里我们使用的原字符串string是一个不可变的字符串。在这种情况下，我的可以看到我们创建的strong特性的对象，跟copy特性的对象,它们所指向的地址都是同一个地址`0x109eff098`，也就是我们使用的不可变字符串`NSString *originStr = @"iOS";`它所指向的地址。我们可以开启MRC模式，打断点调试，查看当前定义这个不可变字符串originStr的引用计数，可以发现执行完操作后`self.theStrongStr = originStr;self.theCopyStr = originStr;`，originStr的引用计数发生了改变1->3。每次执行都使原来的字符串originStr对象的引用计数+1。

**testMutabelString：**
&#160; &#160; &#160; &#160;在testMutabelString方法里面我们使用的原字符串string是一个可变的字符串`NSMutableString *originStr = [NSMutableString stringWithFormat:@"iOS"];`。可以看到输出结果，使用strong特性的对象仍然指向原字符串originStr的地址，而使用copy特性的对象，所指向的是一个新的地址。其实就是copy特性的对象对原字符串originStr进行了深考贝，并指向了这个这个新的地址。我们开启MRC模式，打断点调试，查看到在执行操作后，originStr对象的引用计数1->2，而_theCopyStr对象的引用计数为1。这也就验证copy创建了一个新对象的说法。
&#160; &#160; &#160; &#160;在testMutabelString方法中，不管我们如何修改originStr字符串，_theStrongStr所指向的地址也是跟着originStr字符串指向的地址变动的，这也就证明了_theStrongStr的类型实际上是可变类型NSMutableString，而不是NSString。同理_theCopyStr是指向一个新创建的对象，是不可以改变的。

****

**归纳总结：**
      &#160; &#160; &#160; &#160;当原字符串是NSString类型时，由于它是不可变类型的，不管是使用strong特性，还是copy特性的对象，它们所指向的地址都跟原字符串是一样的，都指向原字符串对象。也就是说当原字符串是NSString类型时，copy特性的操作，只是做了一次浅拷贝，只是增加了指针指向原字符串所指向的地址。

&#160; &#160; &#160; &#160;当原字符串是NSMutableString类型时，strong特性对象只是增加了原字符串的引用计数，但是copy特性对象则是对原字符串进行了深拷贝，创建了一个新对象，并且指向了这个新对象。此时，copy特性对象是NSString类型的不可变的,strong特性对象是NSMutableString类型的可变的。

&#160; &#160; &#160; &#160;关于在声明NSString属性时，我们是要选择strong特性，还是选择copy特性，是需要通过开发过程中的实际情况来选择的。但是我们在大多数情况下，在生命NSString属性时，都是希望其不被改变，防止数据出错。所以大多数情况下还是选择copy特性，从而来避免一些无法预估的bug。在补充一下，当原字符串是NSMutableString类型，也就是可变类型的时候，strong特性操作只是增加了原字符串的引用计数，而copy特性操作则是进行深拷贝，所以在copy会耗费更多的内存资源跟性能。而对NSString类型不可变的，就不会有这种问题，但是基于现在这么强大的手机处理器性能，这些应该也不是什么大问题。


