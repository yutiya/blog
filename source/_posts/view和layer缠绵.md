title: view和layer缠绵
date: 2015-11-04 15:16:14
categories:
- iOS
- view
tags:
- view
---

**view 的 frame bounds center**  

要先讲解frame,还是得讲解bounds;     

当一个view添加到viewcontroller.view时,其实是基于viewcontroller.view的坐标系;      

就像viewcontrolelr.view添加到uiwindow,其实是基于uiwindow的坐标系;     

view的bounds属性就是添加子view的时候,给子视图参照的坐标系;    

该坐标系是由bounds和center计算,frame只是方便描述这两个值而已 

<!-- more -->

例如:
```
坐标系A [viewcontroller.view的bounds 坐标原点(0,0)]

view.frame = (0,0, 100,100) [view在坐标系A中的坐标和大小]
view.center = (50,50) [view的中心在坐标系A中的坐标]
view.bounds = (0,0, 100,100) [此时坐标系B(view.bounds所计算的坐标系)和坐标系A重叠]
    坐标系B.原点x = view.center.x - view.bounds.size.width/2 - view.bounds.origin.x;
    坐标系B.原点y = view.center.y - view.bounds.size.height/2 - view.bounds.origin.y;
    
    此时坐标系B的原点在坐标系A中为(0,0)

    如果你不相信,再添加view1到view中,修改view.bounds.origin为(10,10),view1一定向左移,因为坐标系B的原点向左移动了,view1是基于坐标系B的
    
```

所以根据上面所分析的,得出结论:    

- 当给view添加子view1,子view1将参考坐标系B(view.bounds和view.center所计算的坐标系)
- 坐标系B的坐标原点,受center的值和bounds的值的影响
- bounds的值和center的值由frame计算而来,反过来center值和bounds值的改变不会影响frame
- frame只是便于描述点+宽高,产生一个矩形view而已,内部其实是center和bounds.size来控制view
    ```
        frame = CGRectMake(0, 0, 100, 100);
        //or
        bounds = CGRectMake(0, 0, 100, 100);
        center = CGPointMake(50, 50);

        所以view其实是在坐标系A中确定了中点,再确定宽高,然后就看到了图形
    ```

**layer 的 frame bounds position anchorPoint**  

我们不建议直接设置view.layer为一个新的layer的,而是采用[view addSubview]的形式添加子layer     

此时子layer依然参view.bounds所产生的坐标系B    

而加入anchorPoint的用意是在把layer的"中心"设置在layer的矩形具体的什么位置    

(0,0)左上角, (1,1)右下角, 默认(0.5,0.5) position就是view的center     

比如在桌子上订一个点,默认把矩形的中心放在这个点上,现在改变anchorPoint(改变矩形中和桌子上订点的对应点),看到的矩形的位置将会发生变化

bounds、frame与view一样,bounds会确定一个大小和坐标系,frame只是方便描述bounds和position

anchorPoint(锚点)

![](/images/iOS/viewlayer/1.png)








