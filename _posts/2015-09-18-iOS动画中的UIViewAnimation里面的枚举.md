---
layout: post
title: iOS动画中的UIViewAnimation里面的枚举
date: 2015-09-18 16:56:0.000000000 +09:00
tags: iOS,UIViewAnimation
---

开发过程中常用的关于函数有以下几个:

```
1. [UIView animateWithDuration: animations:^{} completion:^(BOOL finished) {}];

2. [UIView animateWithDuration: animations:^{}];

3. [UIView animateWithDuration: delay: options: animations: completion:^(BOOL finished) {}];
```

&#160; &#160; &#160; &#160;前两个动画的函数的使用,没有太多的可讲.第三个动画,多了一个options选项.需要我们传入一个枚举,这个枚举主要的作用:当前的动画执行随时间函数变化(由快变慢,由慢变快等...效果).可以实现类似淡入淡出的效果.

### 动画过程设置

1. UIViewAniOpinionLayoutSubviews //子视图中控讲将跟父视图中控件使用相同的动画

2. UIViewAnimationOptionAllowUserInteraction //动画时允许用户交互

3. UIViewAnimationOpinionBeginFromCurrentState //重当前状态开始执行动画

4. UIViewAnimationOptionRepeat //动画一直重复播放

5. UIViewAnimationOptionAutoreverse //执行动画回路,前提是设置动画无限重复

6. UIViewAnimationOptionOverrideInheritedDuration //忽略外层动画嵌套的执行时间

7. UIViewAnimationOptionOverrideInheritedCurve //忽略外层动画嵌套的时间变化曲线

8. UIViewAnimationOptionAllowAnimatedContent //通过改变属性和重绘实现动画效果，如果key没有提交动画将使用快照

9. UIViewAnimationOptionOverrideInheritedOptions //忽略嵌套继承的选项


### 时间函数曲线相关

1. UIViewAnimationOptionCurveEaseInOut //时间曲线函数，由慢到快

2. UIViewAnimationOptionCurveEaseIn //时间曲线函数，由慢到特别快

3. UIViewAnimationOptionCurveEaseOut //时间曲线函数，由快到慢

4. UIViewAnimationOptionCurveLinear //时间曲线函数，匀速
转场动画它一般是用在这个方法中的：

```
[UIView transitionFromView: toView: duration: options: completion:^(BOOL finished) {}];
```

### 相关的转场动画

1. UIViewAnimationOptionTransitionNone //无转场动画

2. UIViewAnimationOptionTransitionFlipFromLeft //转场从左翻转

3. UIViewAnimationOptionTransitionFlipFromRight //转场从右翻转

4. UIViewAnimationOptionTransitionCurlUp //上卷转场

5. UIViewAnimationOptionTransitionCurlDown //下卷转场

6. UIViewAnimationOptionTransitionCrossDissolve //转场交叉消失

7. UIViewAnimationOptionTransitionFlipFromTop //转场从上翻转

8. UIViewAnimationOptionTransitionFlipFromBottom //转场从下翻转
