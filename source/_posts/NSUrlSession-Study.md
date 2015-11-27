title: NSUrlSession-Study
date: 2015-11-27 12:27:56
categories:
- iOS
- NSUrlSession
tags: iOS
---

## 学习NSUrlSession的使用

我的服务器是搭建在本地的,所以可以看到我访问的都是localhost,数据也是写死的

- 基本使用

```
    //  下面的代码，放进一个按钮点击事件中执行

    NSURLSession *session = [NSURLSession sharedSession];
    NSURLRequest *request = [[NSURLRequest alloc] initWithURL:[NSURL URLWithString:@"http://localhost/studing/1.php"]];
    NSURLSessionDataTask *task = [session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
        NSString *string = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
        NSLog(@"%@", string);
    }];
    [task resume];
```

打印的内容,就是http请求所返回的所有内容

- next,感受异步

```
//按钮点击
- (void)buttonRequestAction:(UIButton *)sender
{
    NSURLSession *session = [NSURLSession sharedSession];
    NSURLRequest *request = [[NSURLRequest alloc] initWithURL:[NSURL URLWithString:@"http://localhost/studing/1.php"]];
    NSURLSessionDataTask *task = [session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
        sleep(3); // 延迟
        NSString *string = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
        NSLog(@"%@", string);
    }];
    [task resume];
    
}
// 按钮点击
- (void)otherAction:(UIButton *)sender
{
    NSLog(@"并没有阻塞主线程");
}
```

点击request请求服务器,再点击另一个按钮的时候,并没有阻塞主线程     
这就是NSUrlSession的强大之处,next,后面继续

