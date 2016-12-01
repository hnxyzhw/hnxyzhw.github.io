---
layout: post
title: iOS自定义debug模式下的NSLog宏输出
date: 2016-11-18 09:52:35.000000000 +09:00
---

在debug模式下的时候需要把所在的类名、方法名、行数等相关信息也打印出来，这样在开发的时候就可以快速定位调试的位置，以及当前的调试信息。对于解决bug来说，这是一个非常有效率的方法。
同时在发布模式下，我们可以不输出打印这些数据，不会造成冗余数据的产生。

先介绍一些参数：
（1) `__VA_ARGS__`是一个可变参数的宏，很少人知道这个宏，这个可变参数的宏是新的C99规范中新增的，目前似乎只有gcc支持（VC6.0的编译器不支持）。宏前面加上##的作用在于，当可变参数的个数为0时，这里的##起到把前面多余的","去掉的作用,否则会编译出错, 你可以试试。
（2)` __FILE__`宏在预编译时会替换成当前的源文件名。
（3)`__LINE__`宏在预编译时会替换成当前的行号。
（4)` __FUNCTION__`宏在预编译时会替换成当前的函数名称。

**最简单的一个例子：**
```
#ifdef DEBUG
  #define MYLog(fmt, ...) NSLog((fmt), ##__VA_ARGS__);
  #else
  #define MYLog(...);
  #endif
```
**输出当前方法名**
```
#define MYMethod(...) NSLog(@"%s", __func__);
```

**整理**
```
#ifdef DEBUG
  #define DLog(fmt, ...) NSLog((@"[文件名:%s]\n" "[函数名:%s]\n" "[行号:%d] \n" fmt), __FILE__, __FUNCTION__, __LINE__, ##__VA_ARGS__);
  #define DeBugLog(fmt, ...) NSLog((@"%s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
  #define NSLog(...) NSLog(__VA_ARGS__);
  #define MyNSLog(FORMAT, ...) fprintf(stderr,"[%s]:[line %d行] %s\n",[[[NSString stringWithUTF8String:__FILE__] lastPathComponent] UTF8String], __LINE__, [[NSString stringWithFormat:FORMAT, ##__VA_ARGS__] UTF8String]);
  #else
  #define DLog(...)
  #define DeBugLog(...)
  #define NSLog(...)
  #define MyNSLog(FORMAT, ...) nil
  #endif

```


