title: iOS7新特性UIDynamic 
date: 2015-09-14 15:48:35
categories:
- iOS
- UIDynamic
tags: UIDynamic
---

## iOS-UIDynamic    

### 初试    

UIDynamic是iOS7引入的UIKit动力学,目的是将2D物理引擎引入UIKit.    
最明显的就是短信界面,拖动的时候有物理效果.    
通常的时候我们仅使用CA和UIView的动画即可,除非我们需要引入非常逼真的交互设计的时候才使用     
来看看新的基本概念:    

- UIDynamicItem:用来描述一个力学物体的状态,其实就是实现了UIDynamicItem委托的对象,或者抽象为有面积有旋转的质点.     
- UIDynamicBehavior:动力行为的描述,用来指定UIDynamicItem应该如何运动,定义适用的物理规则.一般使用该类的子类对象来对一组UIDynamicItem对象应该遵守的行为规则进行描述.    
- UIDynamicAnimator:动画的播放者,动力行为(UIDynamicBehavior)的容器,添加到容器的行为将发挥作用
- ReferenceView:等同于力学参考系,当添加力学的UIView是ReferenceView的子view或就是它本身的时候,动力就会发生作用.但如果是它本身,虽然能发挥作用,但这个力学仿真就不逼真了    

喵神原文: http://onevcat.com/2013/06/uikit-dynamics-started/ onevcat     

<!-- more -->

写断代码演示下:    

``` code
    @interface ViewController ()

    @property (nonatomic, strong) UIDynamicAnimator *animator;

    @end

    @implementation ViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        
        UIView *aView = [[UIView alloc] initWithFrame:CGRectMake(100, 50, 100, 100)];
        aView.backgroundColor = [UIColor lightGrayColor];
        [self.view addSubview:aView];
        
        UIDynamicAnimator *animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];
        UIGravityBehavior *gravityBeahvior = [[UIGravityBehavior alloc] initWithItems:@[aView]];
        [animator addBehavior:gravityBeahvior];
        self.animator = animator;
        
    }
```

![](/images/iOS/uidynamic/lesson/1.gif)

```
    UIDynamicAnimator *animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];
    ===>
    UIDynamicAnimator *animator = [[UIDynamicAnimator alloc] initWithReferenceView:aView];
```

比第一个要快,这就不逼真了,而且这样写并不对     

![](/images/iOS/uidynamic/lesson/2.gif)

接下来就是物理仿真了,有重力,得有碰撞啊,这样无界的就没有意义了     
碰撞也有一个`UIDynamicBehavior`的子类`UICollisionBehavior`.    

``` code
    - (void)viewDidLoad {
        [super viewDidLoad];
        
        UIView *aView = [[UIView alloc] initWithFrame:CGRectMake(100, 50, 100, 100)];
        aView.backgroundColor = [UIColor lightGrayColor];
        aView.transform = CGAffineTransformRotate(aView.transform, 45);
        [self.view addSubview:aView];
        
        UIDynamicAnimator *animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];
        UIGravityBehavior *gravityBeahvior = [[UIGravityBehavior alloc] initWithItems:@[aView]];
        [animator addBehavior:gravityBeahvior];
        self.animator = animator;
        
        UICollisionBehavior *collisionBehavior = [[UICollisionBehavior alloc] initWithItems:@[aView]];
        collisionBehavior.translatesReferenceBoundsIntoBoundary = YES;
        [animator addBehavior:collisionBehavior];
    }
```

在里边加一了一个旋转角,是不是很有意思    

![](/images/iOS/uidynamic/lesson/3.gif)

`translatesReferenceBoundsIntoBoundary`设置以self.view为边界.`setTranslatesReferenceBoundsIntoBoundaryWithInsets:`方法也可以,更复杂的`addBoundaryWithIdentifier:forPath:`来添加UIBezierPath,或者`addBoundaryWithIdentifier:fromPoint:toPoint:`来添加一条线段为边界     
可以设置collisionBehavior的代理,监听事件.具体的自己看看代码     

- UIAttachmentBehavior描述一个view和一个锚相连接的情况
- UISnapBehavior描述view吸附的情况
- UIPushBehavior描述对view施加力的情况
- UIDynamicItemBehavior相当于自定义描述了,摩擦、阻力等一大堆我不知道的.











