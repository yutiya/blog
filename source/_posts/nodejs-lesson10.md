title: Nodejs第十课
date: 2015-08-31 10:51:39
categories:
- nodejs
- lesson
tags: lesson
---

## benchmaek怎么写

### 目标

有一个字符串`var number = '100'`,我们要将它转换成Number类型的100    
三个选项: + , parseInt, Number    
测试哪种方法更快    

### 知识点

- 学习使用benchmark库
- 学习使用 http://jsperf.com/ 分享你的benchmark

### 课程内容

首先得有一个benchmark库, https://github.com/bestiejs/benchmark.js     
貌似很久没更新的样子,依旧可以使用    
按照实例跟着写:    

<!-- more -->

``` code
    var int1 = function (str) {
        return +str;
    };

    var int2 = function (str) {
        return parseInt(str, 10);
    };

    var int3 = function (str) {
        return Number(str);
    };

    var Benchmark = require('benchmark');
    var suite = new Benchmark.Suite;

    var number = '100';

    suite
        .add('+', function(){
            int1(number);
        })
        .add('parseInt', function(){
            int2(number);
        })
        .add('Number', function(){
            int3(number);
        })
        //每个测试完成后,输出
        .on('cycle', function(event){
            console.log(String(event.target));
        })
        .on('complete', function(){
            console.log('Fastest is ' + this.filter('fastest').pluck('name'));
        })
        //默认勾选async
        .run({ 'async' : true });
```

我们实现了三个函数,然后添加测试    

![](/images/nodejs/lesson10/1.png)

### 在线分享

如果需要在线分享你的js benchmark,进入这个网站: http://jsperf.com/ .     
比如alsotang分享的 http://jsperf.com/math-perf-alsotang    

