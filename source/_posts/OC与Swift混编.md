title: Swift和OC混编
date: 2015-10-10 14:41:24
categories:
- swift
- lesson
tags: swift
---

## Swift和OC混编


Swift语言,抱歉,博主会的不多,今天来简单的讲解一下OC与Swift的混编    
做得不多,我们只要做到能相互调用就好了    
就做世界上最伟大的程序`Hello World`

<!-- more -->

打开Xcode:    

![](/images/iOS/OCANDSwift/1.png)

来个简单的命令行"Hello World":    

![](/images/iOS/OCANDSwift/2.png)

![](/images/iOS/OCANDSwift/3.png)

新建一个OC类:    

![](/images/iOS/OCANDSwift/4.png)

YES Xcode自动帮我们创建桥接swift和OC的头文件:

![](/images/iOS/OCANDSwift/5.png)

**开始写点代码叭**:     

HelloOC.h:    

```
#import <Foundation/Foundation.h>

@interface HelloOC : NSObject

- (void)sayHelloWorld;

@end
```

HelloOC.m:     

```
#import "HelloOC.h"

@implementation HelloOC

- (void)sayHelloWorld
{
    NSLog(@"Hello World!");
}

@end
```

OCSwift-Bridging-Header.h:

```
#import "HelloOC.h"     //在swift文件中调用OC对象,需要导入
```

main.swift:     

```
import Foundation

var hello = HelloOC()
hello.sayHelloWorld()
```

来看看目录结构,运行,没有问题

![](/images/iOS/OCANDSwift/6.png)

新建一个Swift类,用于在OC类中调用:    

![](/images/iOS/OCANDSwift/7.png)

HelloSwift.swift:     

```
import Foundation

class HelloSwift: NSObject {
    
    func sayHello()
    {
        println("Swift Hello")
    }
}
```

OC调用swift会生成一个隐藏的头文件,头文件名称为`项目名-Swift.h`     
`#import "OCSwift-Swift.h"`导入

如果在`HelloOC.h`中导入该头文件会报错     

![](/images/iOS/OCANDSwift/8.png)

在h文件中改使用`@class 类名;`引入

HelloOC.h:    

```
#import <Foundation/Foundation.h>

@class HelloSwift;

@interface HelloOC : NSObject
{
    HelloSwift *swift;
}

- (void)sayHelloWorld;

@end
```
HelloOC.m:     

```
#import "HelloOC.h"
#import "OCSwift-Swift.h"

@implementation HelloOC

- (id)init
{
    if (self = [super init]) {
        swift = [[HelloSwift alloc] init];
    }
    return self;
}

- (void)sayHelloWorld
{
    NSLog(@"Hello World!");
    [swift sayHello];
}

@end
```

运行,没错,这样混编就搞定了     

另外,该桥接的头文件,在这里配置,可以自己创建该头文件并导入     

![](/images/iOS/OCANDSwift/9.png)

该配置指向该头文件


