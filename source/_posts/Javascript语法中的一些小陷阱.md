title: Javascript语法中的一些小陷阱
date: 2015-07-31 14:59:31
categories:
- Javascript
tags: Javascript
---

##  Javascript语法中的一些小陷阱

原来这样写创建出来的obj是一个对象   
在javascript中没有map噢(也就是在别的语言中常常使用的键值对)   

``` code
var obj = {
        username: 'yutiya',
        age: 18
    };
```

直接打印上面创建的对象,会打印出   
在Node.js中还不会打印出前面的Object

``` code
    console.log(obj);
    结果 >>> :
        Object {username: "yutiya", age: 18}
```

如果这样呢?因为它真的是Javascript对象,我也是现在才知道,不晚!

``` code
    console.log('obj:' + obj);
    结果 >>> :
        obj:[object Object]
```

<!-- more -->

下面来一段标准的JSON字符串   
eval() 函数使用的是 JavaScript 编译器,可解析 JSON 文本   
然后生成 JavaScript 对象.必须把文本包围在括号中,这样才能避免语法错误:   

``` code
    var txt = '{ "employees" : [' +
                '{ "firstName":"Bill" , "lastName":"Gates" },' +
                '{ "firstName":"George" , "lastName":"Bush" },' +
                '{ "firstName":"Thomas" , "lastName":"Carter" } ]}';
    var jsonT = eval('(' + txt + ')');
```

提示: eval() 函数可编译并执行任何 JavaScript 代码。这隐藏了一个潜在的安全问题。   
   
使用 JSON 解析器将 JSON 转换为 JavaScript 对象是更安全的做法。   
JSON 解析器只能识别 JSON 文本,而不会编译脚本。
在浏览器中,这提供了原生的 JSON 支持,而且 JSON 解析器的速度更快。
[较新的浏览器和最新的 ECMAScript (JavaScript) 标准中均包含了原生的对 JSON 的支持。](http://www.w3school.com.cn/json/json_eval.asp)   

附上一些可用的代码,如果一些古老的浏览器,可以下载json2.js   
作者没用过,自己上百度谷歌一下   

``` text
JSON.stringify() // 将Javascript对象转换成json格式的字符串
JSON.parse()    //  将JSON格式的字符串转换成Javascript对象
```

不过 js 这门渣渣语言本来就乱嘛,什么变量提升（[传送门](http://www.cnblogs.com/damonlan/archive/2012/07/01/2553425.html) ）啊,没有 main 函数啊,变量作用域啊,数据类型常常简单得只有数字、字符串、哈希、数组啊,   这一系列的问题,都不是事儿。   

作者是菜鸟中的菜鸟,写博客慢慢记录下来!