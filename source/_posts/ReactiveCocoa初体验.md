title: ReactiveCocoa初体验
date: 2015-08-12 15:48:14
tags:
- iOS
- ReactiveCocoa
tags: ReactiveCocoa
---

## Reactive的学习和使用

- 原文链接:http://www.raywenderlich.com/62699/reactivecocoa-tutorial-pt1    
- 下载起始项目:[start project](http://cdn2.raywenderlich.com/wp-content/uploads/2014/01/ReactivePlayground-Starter.zip)    
- 删除测试target,用不上,这样项目便不会报错
- 在xcodeproj同级目录下创建文件Podfile,使用CocoaPods安装Reactive库

Podfile文件内容:    

``` code
    platform :ios, '7.0'

    pod 'ReactiveCocoa', '2.1.8'
```

运行` $ pod install`

![](/images/iOS/reactivecocoa/1.png)

安装完成后使用xcworkspace文件打开项目    

在**RWViewController.m**文件    

``` code
    #import <ReactiveCocoa/ReactiveCocoa.h>
```

在viewDidLoad中添加代码    

``` code
    [self.usernameTextField.rac_textSignal subscribeNext:^(id x) {
        NSLog(@"%@", x);
    }];
```

现在我们就能体验到信号编程了    

![](/images/iOS/reactivecocoa/2.png)

<!-- more -->

可以看到我们每次输出一个字符都会产生一个信号,这大概就是信号编程吧    
我们看看信号传递流程    

![](/images/iOS/reactivecocoa/l1.png)

现在我们将添加的代码修改一下    

``` code
    [[self.usernameTextField.rac_textSignal filter:^BOOL(id value) {
        NSString *text = value;
        return text.length > 3;
    }] subscribeNext:^(id x) {
        NSLog(@"%@", x);
    }];
```

![](/images/iOS/reactivecocoa/3.png)

我们可以看到,当满足filter的条件之后,才会产生信号,后面的条件才会继续执行,有一种链式操作的感觉    
我们将上面的代码的完整结构    

``` code
    RACSignal *usernameSoucesSignal = self.usernameTextField.rac_textSignal;
    RACSignal *filteredUsername = [usernameSoucesSignal filter:^BOOL(id value) {
        NSString *text = value;
        return text.length > 3;
    }];
    [filteredUsername subscribeNext:^(id x) {
        NSLog(@"%@", x);
    }];
```

可以看出来,每次文本框内容的改变就会产生一个信号对象,调用信号处理的block,得到结果     
很显然,我们更喜欢上面那种简洁的语法    

- 现在让我们更进一步,看看到底发生了写什么:

``` code
    [[[self.usernameTextField.rac_textSignal map:^id(id value) {
       NSString *text = value;
       return @(text.length);
   }] filter:^BOOL(id value) {
       NSNumber *length = value;
       return [length integerValue] > 3;
   }] subscribeNext:^(id x) {
       NSLog(@"%@", x);
   }];
```

![](/images/iOS/reactivecocoa/4.png)

信号传递流程    

![](/images/iOS/reactivecocoa/l2.png)

我比较笨,假设你跟我一样,并没有发现有什么问题,现在我们再改改代码:

``` code
    [[[self.usernameTextField.rac_textSignal map:^id(id value) {
       NSString *text = value;
       return text;
   }] filter:^BOOL(id value) {
       NSString *text = value;
       return text.length > 3;
   }] subscribeNext:^(id x) {
       NSLog(@"%@", x);
   }];
```

![](/images/iOS/reactivecocoa/5.png)

这下能看懂了吧,map解析字符串然后向下传递,filter通过返回YES来过滤字符串,subscribeNext得到过滤后的字符串,如果没有满足过滤条件,信号将不会向下传递,能理解信号传递的思路了么    

- 创建验证状态信号

``` code
    RACSignal *validUsernameSignal = [self.usernameTextField.rac_textSignal map:^id(id value) {
          NSString *text = value;
          return @([self isValidUsername:text]);
      }];
    
    RACSignal *validPasswordSignal = [self.passwordTextField.rac_textSignal map:^id(id value) {
        NSString *text = value;
        return @([self isValidPassword:text]);
    }];
```

得到验证信号后,解析然后设置文本框背景与用户交互    
不过我们不采用下面的写法,使用更好的写法    

``` code
    [[validPasswordSignal map:^id(id value) {
        NSNumber *passwordValid = value;
        return [passwordValid boolValue] ? [UIColor clearColor] :[UIColor yellowColor];
    }] subscribeNext:^(id x) {
        UIColor *color = x;
        self.passwordTextField.backgroundColor = color;
    }];
```

我们使用宏定义的方式将解析后返回的信号进行绑定处理    

``` code
    RAC(self.passwordTextField, backgroundColor) = [validPasswordSignal map:^id(id value) {
        NSNumber *passwordValid = value;
        return [passwordValid boolValue] ? [UIColor clearColor] : [UIColor yellowColor];
    }];

    RAC(self.usernameTextField, backgroundColor) = [validUsernameSignal map:^id(id value) {
        NSNumber *usernameValid = value;
        return [usernameValid boolValue] ? [UIColor clearColor] : [UIColor yellowColor];
    }];
```

可以看到代码,我们将文本框和背景颜色绑定为一个信号处理,当验证信号解析后将自动设置    
我们来看看通常我们一般这样写    

``` code
    self.usernameTextField.backgroundColor = self.usernameIsValid ? [UIColor clearColor] : [UIColor yellowColor];
    self.passwordTextField.backgroundColor = self.passwordIsValid ? [UIColor clearColor] : [UIColor yellowColor];
```

这个能看懂吧,平常我们是这样写的,只是改变了编程方式,有一种链式操作的感觉,能将信号绑定处理,其实还不错    
信号传递流程是这样的   

![](/images/iOS/reactivecocoa/l3.png)

- 组合信号

在viewDidLoad添加代码:

``` code
    RACSignal *signUpActiveSignal = [RACSignal combineLatest:@[validUsernameSignal, validPasswordSignal] reduce:^id(NSNumber *usernameValid, NSNumber *passwordValid){
        return @([usernameValid boolValue] && [passwordValid boolValue]);
    }];
    
    [signUpActiveSignal subscribeNext:^(NSNumber *signupActive) {
        self.signInButton.enabled = [signupActive boolValue];
    }];
```

将两个信号组合生成一个新的信号进行解析处理    

![](/images/iOS/reactivecocoa/6.png)

我们这样修改后,就可以移除下面的这些无用的代码了        

``` code
  @property (nonatomic) BOOL passwordIsValid;
  @property (nonatomic) BOOL usernameIsValid;

  // handle text changes for both text fields
  [self.usernameTextField addTarget:self
                           action:@selector(usernameTextFieldChanged)
                 forControlEvents:UIControlEventEditingChanged];
  [self.passwordTextField addTarget:self 
                           action:@selector(passwordTextFieldChanged)
                 forControlEvents:UIControlEventEditingChanged];
```

同时可以移除`updateUIState`,`usernameTextFieldChanged`和`passwdTextFieldChanged`方法等并删除多余的代码     
现在的信号传递流程是这样的    

![](/images/iOS/reactivecocoa/l4.png)

现在代码是不是更简洁,更方便

- 下一步,将其余的业务逻辑也使用信号处理

打开`Main.storyboard`,删除绑定的`sign up`按钮事件    
继续在viewDidLoad中添加事件    

``` code
    [[self.signInButton rac_signalForControlEvents:UIControlEventTouchUpInside] subscribeNext:^(id x) {
        NSLog(@"button clicked");
    }];
```

可以看到下面的效果,事件也可以绑定为信号,感觉好6

![](/images/iOS/reactivecocoa/7.png)

现在我们可以移除`signInButtonTouched:`事件,并创建信号    
增加一个方法    

``` code
  - (RACSignal *)signInSingal
  {
      return [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {
         [self.signInService signInWithUsername:self.usernameTextField.text password:self.passwordTextField.text complete:^(BOOL success) {
             [subscriber sendNext:@(success)];
             [subscriber sendCompleted];
         }];
          return nil;
      }];
  }
```

在viewDidLoad中添加代码    

``` code
    [[[self.signInButton rac_signalForControlEvents:UIControlEventTouchUpInside] map:^id(id value) {
        return [self signInSingal];
    }] subscribeNext:^(id x) {
        NSLog(@"Singal in result %@", x);
    }];
```

![](/images/iOS/reactivecocoa/8.png)

信号传递流程:    

![](/images/iOS/reactivecocoa/l5.png)

下面来修改一下代码:    

``` code
    [[[[self.signInButton rac_signalForControlEvents:UIControlEventTouchUpInside] map:^id(id value) {
        return [self signInSingal];
    }] flattenMap:^RACStream *(id value) {
        return [self signInSingal];
    }] subscribeNext:^(id x) {
        NSLog(@"Sign in result %@", x);
    }];
```

![](/images/iOS/reactivecocoa/9.png)

当点击按钮时验证通过得到1,验证失败得到0    
现在我们让验证通过得到1时跳转    

``` code
    [[[[self.signInButton rac_signalForControlEvents:UIControlEventTouchUpInside] map:^id(id value) {
        return [self signInSingal];
    }] flattenMap:^RACStream *(id value) {
        return [self signInSingal];
    }] subscribeNext:^(NSNumber *signedIn) {
        BOOL success = [signedIn boolValue];
        self.signInFailureText.hidden = success;
        if (success) {
            [self performSegueWithIdentifier:@"signInSuccess" sender:self];
        }
    }];
```

现在,我们就使用ReactiveCocoa替代了原来的业务处理,比较简洁,但是也比较难理解    
效果图:    
![](/images/iOS/reactivecocoa/r1.gif)

修改代码,在信号传递中加一个过程    

``` code
    [[[[self.signInButton rac_signalForControlEvents:UIControlEventTouchUpInside] doNext:^(id x) {
        self.signInButton.enabled = NO;
        self.signInFailureText.hidden = YES;
    }] flattenMap:^RACStream *(id value) {
        return [self signInSingal];
    }] subscribeNext:^(NSNumber *signedIn) {
        self.signInButton.enabled = YES;
        BOOL success = [signedIn boolValue];
        if(success) {
            [self performSegueWithIdentifier:@"signInSuccess" sender:self];
        }
    }];
```

![](/images/iOS/reactivecocoa/l6.png)

再运行看看,细节更加完善
![](/images/iOS/reactivecocoa/r2.gif)