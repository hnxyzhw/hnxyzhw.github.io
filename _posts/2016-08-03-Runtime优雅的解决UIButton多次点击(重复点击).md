　在实际开发中我们，点击一个button按键时，需要触发一个事件去执行。用户在正常操作情况下，单次点击时，button只会响应一次点击。但是如果用户多次点击一个button，那么就会引起这个事件被多次执行，导致一些bug的出现。
如何优雅解决的这个问题呢？今天我们来使用Runtime来解决UIButton重复点击的问题。
　首先新建一个分类category，继承于UIControl,名字自己定义。
　UIControl+ZHW.h(.h文件)
```
@interface UIControl (ZHW)
@property (nonatomic, assign) NSTimeInterval zhw_acceptEventInterval;//添加点击事件的间隔时间

@property (nonatomic, assign) BOOL zhw_ignoreEvent;//是否忽略点击事件,不响应点击事件
@end
```
　UIControl+ZHW.m(.m文件)在使用runtime时，需要导入相应的库（objc/runtime.h）

```
@implementation UIControl (ZHW)
static const char *UIControl_acceptEventInterval = "UIControl_acceptEventInterval";

static const char *UIcontrol_ignoreEvent = "UIcontrol_ignoreEvent";

- (NSTimeInterval)zhw_acceptEventInterval {
    
    return [objc_getAssociatedObject(self, UIControl_acceptEventInterval) doubleValue];
    
}

- (void)setZhw_acceptEventInterval:(NSTimeInterval)zhw_acceptEventInterval {
    
    objc_setAssociatedObject(self, UIControl_acceptEventInterval, @(zhw_acceptEventInterval), OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    
}

- (BOOL)zhw_ignoreEvent {
    
    return [objc_getAssociatedObject(self, UIcontrol_ignoreEvent) boolValue];
    
}

- (void)setZhw_ignoreEvent:(BOOL)zhw_ignoreEvent {
    
    objc_setAssociatedObject(self, UIcontrol_ignoreEvent, @(zhw_ignoreEvent), OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    
}

+ (void)load {
    
    Method a = class_getInstanceMethod(self, @selector(sendAction:to:forEvent:));
    
    Method b = class_getInstanceMethod(self, @selector(__zhw_sendAction:to:forEvent:));
    
    method_exchangeImplementations(a, b);
    
}

- (void)__zhw_sendAction:(SEL)action to:(id)target forEvent:(UIEvent *)event {
    
    if (self.zhw_ignoreEvent) return;
    
    if (self.zhw_acceptEventInterval > 0) {
        
        self.zhw_ignoreEvent = YES;
        
        [self performSelector:@selector(setZhw_ignoreEvent:) withObject:@(NO) afterDelay:self.zhw_acceptEventInterval];
        
    }
    
    [self __zhw_sendAction:action to:target forEvent:event];
    
}

@end
```
　在需要用到地方导入UIControl+ZHW.h头文件设置button的点击时间间隔

```
@interface ViewController ()
@property (weak, nonatomic) IBOutlet UIButton *button;
@property (weak, nonatomic) IBOutlet UIView *colorView;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    self.button.zhw_ignoreEvent = NO;
    self.button.zhw_acceptEventInterval = 3.0;
}
- (IBAction)runtimeAction:(UIButton *)sender {
    NSLog(@"----run click");
    [UIView animateWithDuration:3 animations:^{
        
        self.colorView.center = CGPointMake(200, 500);
        
    } completion:^(BOOL finished) {
        
        self.colorView.center = CGPointMake(95, 85);
        
    }];
}
- (IBAction)buttonAction:(UIButton *)sender {
    NSLog(@"------comm click");
    [UIView animateWithDuration:3 animations:^{
        
        self.colorView.center = CGPointMake(200, 500);
        
    } completion:^(BOOL finished) {
        
        self.colorView.center = CGPointMake(95, 85);
        
    }];
}

```
　运行demo,可以发现button多次点击的问题得到了解决。在设置button的相应点击事件的时间间隔，在这个 间隔时间内，button只会响应一次点击事件。
　附上demo：[UIButtonMutablieClick](https://github.com/hnxyzhw/UIButtonMutablieClick.git)
