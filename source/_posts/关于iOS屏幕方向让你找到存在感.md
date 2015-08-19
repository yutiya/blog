title: 关于iOS屏幕方向让你找到存在感
date: 2015-08-19 12:18:50
categroies:
- iOS
- orientation
tags:
- orientation
---

## 关于iOS屏幕方向,让你找到方向感

看了这篇文章,可能是我太笨,有可能是作者太牛逼,反正我是没看懂,都没下他写的demo    
写的什么鬼,自己来搞一搞    
原文链接:http://lvwenhan.com/ios/458.html    

新建一个空项目,在target->General->Deployment Info中勾选    

- Protrait
- Landscape Left
- Landscape Right

不然只会支持你所选择的方向(那就没得玩了)    
然后再Main.storyboard中拖入一个按钮,放到左上角一些,why?因为不在界面上放个东西,改变方向你会晕的    
现在run,手动改变方向`command+⬅️`,效果给你个截图    

![](/images/iOS/screenOrientation/1.gif)

可以看到屏幕的方向会随着设备的方向而改变,Landscape Left(顺时针转,home键在左),如果用户锁定了屏幕旋转,那就不用讨论设备转向的情况下屏幕跟随转向的问题了        

``` code
    //file ViewController.m
    ...
    - (BOOL)shouldAutorotate
    {
        return NO;
    }
    ...
```

显而易见的,这个方法控制我们的页面是否跟随设备转动而改变方向    

![](/images/iOS/screenOrientation/2.gif)

<!-- more -->

我们研究这个的目的是不管用户是否锁定屏幕转向,我们在打开新页面的时候,也需要手动控制viewController显示的方向    
现在我们新建一个SecondViewController类,往Main.storyboard中拖入一个viewController,绑定SecondViewController,Storyboard ID:secondVC    
写代码跳转,像这样...    

![](/images/iOS/screenOrientation/3.gif)

加入这个代码到SecondViewController.m中,强制设置屏幕方向,不一定能通过苹果审核噢

``` code
- (void)viewDidLoad {
    [super viewDidLoad];
    
    if([[UIDevice currentDevice]respondsToSelector:@selector(setOrientation:)]) {
        [[UIDevice currentDevice]performSelector:@selector(setOrientation:)
                                      withObject:@(UIInterfaceOrientationLandscapeLeft)];
    }
    
}
```

![](/images/iOS/screenOrientation/4.gif)

时光不能倒流,回不去,对吧,呵呵,强制设置,你可以试试在退出的时候再设置回来,对吧    

``` code
    - (BOOL)shouldAutorotate
    {
        return NO;
    }
```

![](/images/iOS/screenOrientation/5.gif)

如果在加入这个,呵呵,上面那段代码不是扯淡?

去掉UIDeveice的代码,继续搞...    

``` code
    //file viewController.m的代码是这样
    - (BOOL)shouldAutorotate
    {
        return NO;
    }
    - (NSUInteger)supportedInterfaceOrientations
    {
        return UIInterfaceOrientationMaskPortrait;
    }

    //file SecondViewController.m的代码是这样
    - (BOOL)shouldAutorotate
    {
        return NO;
    }
    - (NSUInteger)supportedInterfaceOrientations
    {
        return UIInterfaceOrientationMaskLandscapeLeft;
    }
```

![](/images/iOS/screenOrientation/6.gif)

百度了一下很多的帖子,实际写代码,不用重写以下方法,why?自我理解,因为我们是自己手动管理屏幕方向,由我们控制每个viewController的显示方向

``` code
    - (UIInterfaceOrientation)preferredInterfaceOrientationForPresentation
    {
        return UIInterfaceOrientationLandscapeLeft;
    }
```

在我们使用的过程中,通常我们是嵌套viewController,比如N+V或者T+N+V等等    
在Main.storyboard中给ViewController套一个navigationController     
Editor->Embed In->Navigation Controller    
然后run    

![](/images/iOS/screenOrientation/7.gif)

我们发现,viewController会跟随设备旋转了,新建一个navigationController类,在该类里控制,记住与Main.storyboard中绑定    
``` code
    //file ViewNavigationController.m
    // 这是一个navigationController
    - (BOOL)shouldAutorotate
    {
        return NO;
    }
    - (NSUInteger)supportedInterfaceOrientations
    {
        return UIInterfaceOrientationMaskPortrait;
    }
```

此时的Main.storyboard:    

![](/images/iOS/screenOrientation/1.png)

效果:    

![](/images/iOS/screenOrientation/8.gif)

看对了吧,此时不管你有没有注释ViewController中的代码，都不会有问题    
TabBarController也一样    
所以这个就好理解了吧...,下面的是swift代码,你看不懂?抱歉,我不怎么会,能看懂    

``` code
    // tabbarController
    func tabBarControllerSupportedInterfaceOrientations(tabBarController: UITabBarController) -> UIInterfaceOrientationMask {
        return self.selectedViewController!.supportedInterfaceOrientations()
    }
    func tabBarControllerPreferredInterfaceOrientationForPresentation() -> UIInterfaceOrientation {
        return self.selectedViewController!.preferredInterfaceOrientationForPresentation()
    }
    // navigationController
    override func supportedInterfaceOrientations() -> UIInterfaceOrientationMask {
        return self.visibleViewController!.supportedInterfaceOrientations()
    }
    override func preferredInterfaceOrientationForPresentation() -> UIInterfaceOrientation {
        return self.visibleViewController!.preferredInterfaceOrientationForPresentation()
    }
```

总结一下.
- 设备的方向和状态栏所在的位置不一样噢(这个知道,不过忘记了)
- viewController是否支持自动旋转是由自身控制
- 如果嵌套的话由父viewCotroller来控制




