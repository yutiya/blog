title: httpbody中字符编码问题-iOS解决方案
date: 2015-07-30 14:35:00
categories:
- iOS
tags: httpbody字符编码
---

## httpbody中字符编码问题

在工作中遇到一个问题,本来可以直接将字典转换成json,再转换成nsdata,设置到httpbody中   
就可以请求服务器了,可是服务器不方便解析(其实是做服务器开发的不知道怎么解析收到的数据|_|)   
最后只能将字典遍历键值对变成查询字符串的样子设置在body中   

For Example:   

``` text
    {
        "username" : "yutiya",
        "age" : 18
    }
    变成:
        username=yutiya&age=18
```

通常上传到服务器的数据都需要编码一次,不过我上传的数据全是[a-zA-Z0-9]等等,不用编码也是可以的   
可是问题出现了,上传到服务器的数据中有+号,看了[阮一峰的博客](http://www.ruanyifeng.com/blog/2010/02/url_encoding.html),从中可以看到+在服务器会被自动解析成空格   
简直是见了鬼,然后就得改代码呗

``` code
    NSString *str = @"username=yutiya&lock=abc+bbc+ccc";
    NSLog(@"%@", [str stringByReplacingPercentEscapesUsingEncoding:NSUTF8StringEncoding]);
    console >>>:
        2015-07-30 14:51:07.212 httpbody中的字符编码[6470:156038] username=yutiya&lock=abc+bbc+ccc
```

然而并没有什么卵用,好,我换了一个方法   

``` code
    NSString *str = @"username=yutiya&lock=abc+bbc+ccc";
    NSLog(@"%@", [str stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding]);
    console >>>:
        2015-07-30 14:52:50.828 httpbody中的字符编码[6488:156742] username=yutiya&lock=abc+bbc+ccc
```

然而也没有什么卵用,在阮一峰的博客中描述了,只有采用对url进行编码才会把+号等字符进行编码,+号编码后是`%2B`,我承认我当时就2B了

上html代码就知道对url编码是什么样子了:

``` code
    <html>
    <body>
    <script type="text/javascript">
        document.write(encodeURIComponent("http://www.baidu.com/")+ "<br />")
        document.write(encodeURIComponent("http://www.baidu.com/My first/")+ "<br />")
        document.write(encodeURIComponent(" ") + "<br />");
        document.write(encodeURIComponent("+"))
    </script>
    </body>
    </html>
    >>>:
        http%3A%2F%2Fwww.baidu.com%2F
        http%3A%2F%2Fwww.baidu.com%2FMy%20first%2F
        %20
        %2B
```

知道了吧,你这样传到服务器,服务器就能收到了噢,因为服务器会自动解码,具体以什么方式解码,请看阮一峰的博客,大师呢!   
NEXT,那OC代码怎么写呢?磨叽磨叽,我来给你瞅瞅   

``` code
    NSString *str = @"username=yutiya&lock=abc+bbc+ccc";
    NSString *encodeStr = (NSString *)CFBridgingRelease(CFURLCreateStringByAddingPercentEscapes(kCFAllocatorDefault, (CFStringRef)str,  NULL, (CFStringRef)(@":/?#[]@!$ &'()*+,;=\"<>%{}|\\^~`"), kCFStringEncodingUTF8));
    NSLog(@"%@", encodeStr);
    console >>>:
        2015-07-30 15:04:50.196 httpbody中的字符编码[6589:162380] username%3Dyutiya%26lock%3Dabc%2Bbbc%2Bccc
```

是不是很神奇,就出来了,上面的代码在ARC环境下调用,可以在服务器进行测试噢   
会编码`@":/?#[]@!$ &'()*+,;=\"<>%{}|\\^~`"`指定的这些字符噢   

好了,说实话涉及到CoreFoundation对象,内存管理就更麻烦了,没事儿,给你们找了一篇博客参考   
[Objective-C 和 Core Foundation 对象相互转换的内存管理总结](http://blog.csdn.net/yiyaaixuexi/article/details/8553659)   
标题虽长,只用command + c,^_^~

参考一下吧!

我测试了另一个方法   

``` code
        NSString *str = @"username=yutiya&lock=abc+bbc+ccc";
        NSString *encodeStr = (NSString *)CFBridgingRelease(CFURLCreateStringByReplacingPercentEscapesUsingEncoding(kCFAllocatorDefault, (CFStringRef)str, (CFStringRef)(@":/?#[]@!$ &'()*+,;=\"<>%{}|\\^~`"), kCFStringEncodingUTF8));
        NSLog(@"%@", encodeStr);
        console >>>:
            2015-07-30 15:16:42.803 httpbody中的字符编码[6676:167136] username=yutiya&lock=abc+bbc+ccc
```

好像体现出AddingPercentEscapes和ReplacingPercentEscapes的不同了,有问题的发邮件给我噢

