title: exports和module.exports的区别
date: 2015-08-14 17:58:31
categories:
- nodejs
- exports
tags:
- exports
---

## exports和module.exports的区别

我们今天主要来理解exports和module.exports的区别    
写了一些代码,有问题请指教      
 
 ``` code
    // file: app.js
    var a = {name: 'yutiya'};
    var b = a;

    console.log(a);
    console.log(b);

    b.name = 'Yutiya';
    console.log(a);
    console.log(b);

    b = {name: 'test'};

    console.log(a);
    console.log(b); 
 ```
 
 结果:    

 ``` text
    { name: 'yutiya' }
    { name: 'yutiya' }
    { name: 'Yutiya' }
    { name: 'Yutiya' }
    { name: 'Yutiya' }
    { name: 'test' }
 ```
使用b变量修改属性,a变量的属性也会跟着变,说明两个变量使用一块内存,也就是说a和b都是`指针`(c和c++中)类型,指向同一个对象    
给b变量赋值新的对象,b变量指向新的内存区域    

<!-- more -->

下面我们来看看exports和module.exports    

``` code
    //file: testModule.js
    // 什么也不写

    //file: app.js
    var m = require('./testModule');//如果不写./应该在 node_mudules/ 目录下开始找,找不到就没有了
    console.log(m);
```

结果:    

``` text
    {}
```

说明module.exports和exports初始化的时候都是`{}`空    
下一步:    

``` code
    //file: testModule.js
    exports = {name: 'yutiya'};
```

结果:    

``` text
    {}
```

说明引用一个模块,并没有引用exports变量,而是module.exports变量,试试

``` code
    //file: testModule.js
    module.exports = {name: 'yutiya'};
```

结果:    

``` text
    { name: 'yutiya' }
```

现在我们知道了,模块导入对象或方法均是在module.exports中    
我们继续发现    

``` code
    //file: testModule.js
    exports.name = 'yutiya';
```

结果:

``` text
    { name: 'yutiya' }
```

现在能知道,一开始module.exports和exports均指向同一个对象,给对象设置属性,不管使用两个变量中的哪一个均可,可是外部引用只是引用module.exports     
所以我们总结一下,给该模块增加属性,两个均可使用,如果导出模块为方法或者给module.exports重新赋值,那么引用该模块得到的就会是module.exports赋值的内容

``` code
    //file: testModule.js
    module.exports = function(){
        console.log('testModule');
        console.log(exports);
    };

    //file: app.js
    var m = require('./testModule');
    m();
```

结果是这样:    

``` text
    testModule
    {}
```

文章写得不好,如果您还是毕竟晕,不如上百度谷歌一下
