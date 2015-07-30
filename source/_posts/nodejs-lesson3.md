title: nodejs lesson3
date: 2015-07-29 18:42:22
categories:
- nodejs
- express
tags: express
---

## 使用superagent和cheerio完成简单爬虫

- 使用superagent抓取网页
- 使用cheerio分析网页

### 课程内容

Node.js 异步特性特别厉害,想要学习,利用爬虫的场景比较适合.   

[原文](https://github.com/alsotang/node-lessons/tree/master/lesson3)中介绍了爬github有rate limit限制,作者是菜鸟,不懂,所以也爬了Node.js开源社区[CNode社区](https://cnodejs.org)   

使用了三个依赖,分别是express、superagent和cheerio

[superagent](http://visionmedia.github.io/superagent/)是一个http方面的库,可以发起get或post请求   
[cheerio](https://github.com/cheeriojs/cheerio)大家可以理解为Node.js版的jQuery,使用方式和jQuery一样   
两者均为链式操作,用起来特别爽

走你:   
``` bash
$ npm init  // 初始化
$ npm install <packeagename> --save 
$ touch app.js
$ vi app.js
```

第一步我们尝试打印出superagent发出的get请求的内容

``` code
    var superagent = require('superagent');
    var cheerio = require('cheerio');
    var express = require('express');
    var app = express();
    app.get('/', function(req, res, next){
        superagent.get('https://cnodejs.org')
            .end(function(err, sres){
                if (err) {
                    return next(err);
                }
                res.send(sres);
            })
    });
    app.listen(3000, function(req, res){
        console.log('app is running at port 3000');
    });
```

<!-- more -->

我们分析网页内容,修改代码

``` code
    app.get('/', function(req, res, next){
        superagent.get('https://cnodejs.org')
            .end(function(err, sres){
                if (err) {
                    return next(err);
                }
                var $ = cheerio.load(sres.text);
                var items = [];
                //  根据网页内容分析
                $('#topic_list .topic_title').each(function(idx, element){
                    var $element = $(element);
                    items.push({
                        title: $element.attr('title'),
                        href: $element.attr('href')
                    });
                });
                res.send(items);
            })
    });
```

试一试,是不是完成了一个小小的爬虫程序?   

下一步我们继续挑战,修改代码,把作者也分析出来,试试吧,下面附上代码   

``` code 
    app.get('/', function(req, res, next){
    superagent.get('https://cnodejs.org')
        .end(function(err, sres){
            if (err) {
                return next(err);
            }
            var $ = cheerio.load(sres.text);
            var items = [];
            $('#topic_list .cell').each(function(idx, element){
                var $element = $(element);
                var $top_title = $element.find('.topic_title');
                var $author = $element.find('.user_avatar img');
                items.push({
                    title: $top_title.attr('title'),
                    href: $top_title.attr('href'),
                    author: $author.attr('title')
                });
            });
            res.send(items);
        })
});
```