之前自定义了navigationBar的背景颜色，升级到iOS10后，发现title,跟leftBarButtonItem不显示。
iOS9 之前的navigationBar的背景是_UINavigationBarBackground，到iOS变成了_UIBarBackground，可以通过xcode查看一下相应的布局。我的解决办法是，分别判断iOS10，iOS9的版本，找到对应的背景图，将起隐藏掉，可以消除分割线。然后重新创建一个视图层，颜色可以自定义，需要注意在添加视图或者更新视图时要放到主线程里，同时这个方法需要在viewWillAppear里调用。

```
#define isIOS9 ([[UIDevice currentDevice].systemVersion intValue]>=9?YES:NO)
#define isIOS10 ([[UIDevice currentDevice].systemVersion intValue]>=10?YES:NO)
```



	
```
#pragma mark - 动态修改状态栏跟顶部导航栏的颜色
-(void)changeNavigationBarBackgroundColor:(UIColor *)barBackgroundColor{
    if ([self.navigationController.navigationBar respondsToSelector:@selector(setBackgroundImage:forBarMetrics:)]){
        NSArray *subviews =self.navigationController.navigationBar.subviews;
        for (id viewObj in subviews) {
            if (isIOS10) {
                //iOS10,改变了状态栏的类为_UIBarBackground
                NSString *classStr = [NSString stringWithUTF8String:object_getClassName(viewObj)];
                if ([classStr isEqualToString:@"_UIBarBackground"]) {
                    UIImageView *imageView=(UIImageView *)viewObj;
                    imageView.hidden=YES;
                }
            }else{
                //iOS9以及iOS9之前使用的是_UINavigationBarBackground
                NSString *classStr = [NSString stringWithUTF8String:object_getClassName(viewObj)];
                if ([classStr isEqualToString:@"_UINavigationBarBackground"]) {
                    UIImageView *imageView=(UIImageView *)viewObj;
                    imageView.hidden=YES;
                }
            }
        }
        UIImageView *imageView = [self.navigationController.navigationBar viewWithTag:111];
        if (!imageView) {
            imageView=[[UIImageView alloc] initWithFrame:CGRectMake(0, -20, self.view.width, 64)];
            imageView.tag = 111;
            [imageView setBackgroundColor:barBackgroundColor];
            dispatch_async(dispatch_get_main_queue(), ^{
                [self.navigationController.navigationBar insertSubview:imageView atIndex:0];
            });
        }else{
            [imageView setBackgroundColor:barBackgroundColor];
            dispatch_async(dispatch_get_main_queue(), ^{
                [self.navigationController.navigationBar sendSubviewToBack:imageView];
            });
            
        }
        
    }
}
```
