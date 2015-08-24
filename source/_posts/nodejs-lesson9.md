title: Nodejs第九课
date: 2015-08-24 09:45:52
categories:
- nodejs
- lesson
tags: lesson
---

## 正则表达式    

### 目标

``` code
    var web_development = "python php ruby javascript jsonp perhapsphpisoutdated";
```

找出其中包含p但不包含ph的所有单词,即    
`[ 'python', 'javascript', 'jsonp']`

### 知识点

- 正则表达式的使用
- js中正则表达式与[pcre](http://en.wikipedia.org/wiki/Perl_Compatible_Regular_Expressions)的区别

### 课程内容

开始这门课之前,先学习   
[正则表达式30分钟入门教程](http://deerchao.net/tutorials/regex/regex.htm)
然后继续学习零宽断言    
[正则表达式之:零宽断言不『消费』](http://fxck.it/post/50558232873) 

很久以前,处理字符串领域的王者当属perl.    
后来出现一个标准叫pcre.     
不过前两个都不算标准,后来出现了正则表达式.js里的正则表达式与pcre不兼容.    
测试自己写的正则表达式可以访问http://refiddle.com/,所见即所得地调试    

讲述js中正则表达式
- js中,对于四种零宽断言,只支持零宽正预测先行断言和零宽度负预测先行断言
- js中,正则表达式后面可以跟三个flag.比如/something/igm.
    - i的意义是不区分大小写
    - g的意义是匹配多个
    - m的意义是^和$可以匹配每一行的开头

<!-- more -->

举几个例子:    

``` code
    /a/.test('A')   // => false
    /a/i.test('A')  // => true
    'hello hell hoo'.match(/h.*?\b)     // => [ 'hello', index: 0, input: 'hello hell hoo' ]
    'hello hell hoo'.match(/h.*?\b/g)   // => [ 'hello', 'hell', 'hoo' ]
    'aaa\nbbb\nccc'.match(/^[\s\S)*?$/g)    // => [ 'aaa\nbbb\nccc' ]
    'aaa\nbbb\nccc'.match(/^[\s\S]*?$/gm)   // => [ 'aaa', 'bbb', 'ccc' ]
```

还有一些别的:    

``` text
    \A  字符串开头(类似^,但不受处理多行选项的影响)
    \Z  字符串结尾或行尾(不受处理多行选项的影响)
    \z  字符串结尾(类似$,但不受处理多行选项的影响)
```

在js中,g flag 会影响`String.prototype.match()`和`RegExp.prototype.exec()`的行为     
`String.prototype.match()`中,返回数据的格式会不一样,加g会返回数组,不加g则返回比较详细的信息    

``` code
    'hello hell'.match(/h(.*?)\b/g) // => [ 'hello', 'hell' ]
    'hello hell'.match(/h(.*?)\b/)  // => [ 'hello', 'ello', index: 0, input: 'hello hell' ]
```

`RegExp.prototype.exec()`中:    

``` code
    /h(.*?)\b/g.exec('hello hell')  // =>   [ 'hello', 'ello', index: 0, input: 'hello hell' ]
    /h(.*?)\b/g.exec('hello hell')  // =>   [ 'hello', 'ello', index: 0, input: 'hello hell' ]
    var re = /h(.*?)\b/g;
    re.exec('hello hell')           // =>   [ 'hello', 'ello', index: 0, input: 'hello hell' ]
    re.exec('hello hell')           // =>   [ 'hell', 'ell', index: 6, input: 'hello hell' ]
```

第三.大家知道.是不可以匹配`\n`的.如果我们想匹配的数据涉及到了跨行:    

```` code
    var mutiline = require('multiline');
    var text = multiline.stripIndent(function(){
        /* 
            head
            ```
            code code2 .code3```
            ```
            foot
        */
    });
````

如果需要获取两个```中的内容,无法使用.匹配\n,所以我们需要找一个元字符还匹配`\n`在内的所有字符    
惯用写法`[\s\S]`     

``` code
    var match1 = text.match(/^```[\s\S]+?^```/gm);
    console.log(match1);
    //这里有一种很骚的写法,[^]与[\s\S]等价
    var match2 = text.match(/^```[^]+?^```/gm);
    console.log(match2);
```

结果:    

``` text
    [ '```\ncode code2 .code3```\n```' ]
    [ '```\ncode code2 .code3```\n```' ]
```


我把学习中写的代码全贴上了,自己测试    

```` code
    var s = 'aaalllsss0tAAAnnn999';
    var re2 = /(\w)\1{2}(?=(\w)\2{2})/g;
    console.log(s.match(re2));

    var s = 'aaalllsss0tAAAnnn999';
    var re1 = /((\w)\2{2})(\w)\3{2}/g;
    console.log(s.match(re1));

    var s = 'aaalllsss0tAAAnnn999';
    var re1 = /((\w)\2{2})(\w)\3{2}/g;
    console.log("s is: " + s);
    console.log();
    var res;
    while(res=re1.exec(s)) {
      console.log("match result: " + res[1] + ".",
       "re1 comsumed: " + res[0],
       "re1.lastindex: " + re1.lastIndex,
       "remain string: " + s.slice(re1.lastIndex));
    }

    var s = 'aaalllsss0tAAAnnn999';
    var re1 = /(\w)\1{2}(?=(\w)\2{2})/g;
    console.log("s is: " + s);
    console.log();
    var res;
    while(res=re1.exec(s)) {
      console.log("match result: " + res[1] + ".",
       "re1 comsumed: " + res[0],
       "re1.lastindex: " + re1.lastIndex,
       "remain string: " + s.slice(re1.lastIndex));
    }

        console.log(/a/.test('A'));
        console.log(/a/i.test('A'));
        console.log('hello hell hoo'.match(/h.*?\b/));
        console.log('hello hell hoo'.match(/h.*?\b/g));
        console.log('aaa\nbbb\nccc'.match(/^[\s\S]*?$/g));
        console.log('aaa\nbbb\nccc'.match(/^[\s\S]*?$/gm));

        console.log('hello hell'.match(/h(.*?)\b/g));
        console.log('hello hell'.match(/h(.*?)\b/));

        console.log(/h(.*?)\b/g.exec('hello hell'));
        console.log(/h(.*?)\b/g.exec('hello hell'));
        var re = /h(.*?)\b/g;
        console.log(re.exec('hello hell'));
        console.log(re.exec('hello hell'));

        var multiline = require('multiline');
        var text = multiline.stripIndent(function(){
            /*
                head
                ```
                code code2 .code3```
                ```
                foot
            */
        });
        var match1 = text.match(/^```[\s\S]+?^```/gm);
        console.log(match1);
        //这里有一种很骚的写法,[^]与[\s\S]等价
        var match2 = text.match(/^```[^]+?^```/gm);
        console.log(match2);
````

目标答案,出自一个牛人之手,我写了好久都没写出来...-_-!!!    

``` code
    var web_development = 'python php ruby javascript jsonp perhapsphpisoutdated';
    var reg1 = /\b(?=\w*p\w*)(?!\w*ph\w*)\w*\b/ig;
    console.log("web_development is: " + web_development);
    console.log(web_development.match(reg1));
    var res;
    while(res = reg1.exec(web_development)) {
      console.log("match result: " + res[1] + ".",
       "reg1 comsumed: " + res[0],
       "reg1.lastindex: " + reg1.lastIndex,
       "remain string: " + web_development.slice(reg1.lastIndex));
    }
```




