---
layout: post
title: iOS-中JS与OC(UIWebView)的交互
date: 2016-07-26 21:26:00.000000000 +09:00
---
项目开发中，会推一些活动供用户参加，活动页面一般都是用h5或者web页面，这活动页面有时候需要跟移动端有交互操作，比如点了一个链接或者button，跳转到app内的某个页面。那么这个时候就需要移动端交互来完成了。

UIWebView是可以捕获当前页面要去加载的url地址，比如当你点击了页面的一个超链接，或者一个图片类型的标签连接，又或者是点击页面的中的button（在点击方法里去加载指定的url）。那么当前的UIWebView就会去加载这个url地址，此时我们可以通过UIWebView的代理方法去拦截这个url。

具体代码如下（UIWebViewDelegate）：

```
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
//判断是否是单击
if (navigationType == UIWebViewNavigationTypeLinkClicked)
{
  NSURL *url = [request URL];
  NSLog(@"----url:%@",[url absoluteString]);
//当你单击页面上的超链接或者button，去加载url时，是可以在这个代理方法里面拦截到url地址的
//既然能够拿到，那么就可以跟写这个web页面或者h5的同事，约定一个url
//比如你的url是：http://www.huodong
  if([[url absoluteString] isEqualToString:@"http://www.huodong"]){
    //如果是你们约定的url,那么就可以在执行你要调转的方法
    return NO;//(NO,表示不去加载这个url地址)
  }else{
    //如果不是,那么就去加载我们的不需要跳转的url连接地址
    return YES;
  }
}
return YES;

}

```

还有另外一种方法也可实现js跟oc的交互（使用了本地的一个html测试），这个需要导入JavaScriptCore.framework

在viewDidLoad方法里：

```
mywebView = [[UIWebView alloc] initWithFrame:CGRectMake(0, 0, self.view.frame.size.width, self.view.frame.size.height)];
[mywebView setDelegate:self];

NSString *path = [[NSBundle mainBundle] pathForResource:@"huodong" ofType:@"html"];

NSString *htmlString = [NSString stringWithContentsOfFile:path encoding:NSUTF8StringEncoding error:nil];

NSString *basePath = [[NSBundle mainBundle] bundlePath];
NSURL *baseURL = [NSURL fileURLWithPath:basePath];
[mywebView loadHTMLString:htmlString baseURL:baseURL];

JSContext *context=[mywebView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];

//注意这个jump方法就是html中点击button后要执行的方法，
/*
	document.getElementById('button').onclick = function(){window.jump(12);
*/
//html中回去掉jump这个方法，并讲12这个整形参数传递过来，这个是可以传多参数的
context[@"jump"] = ^() {
	NSArray *args = [JSContext currentArguments];
	for (id obj in args) {
		NSLog(@"%@",obj);
	}
};
```

可以参考下面这个博客

iOS js oc相互调用（JavaScriptCore）（二）
[js oc相互调用](http://blog.csdn.net/lwjok2007/article/details/47058795)

IOS开发—JS调用OC(通过非URL的方式)
[IOS开发—JS调用OC](http://www.jianshu.com/p/df76cc7a395d)

iOS js与oc交互(js调用oc篇)
[js与oc交互](http://www.jianshu.com/p/4099d9634810)

