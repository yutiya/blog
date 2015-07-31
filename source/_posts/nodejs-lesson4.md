title: nodejs lesson4
date: 2015-07-29 18:42:22
categories:
- nodejs
- lesson
tags: lesson
---

## 使用eventproxy控制并发

### 目标

输出[CNode社区](https://cnodejs.org/ )社区首页的所有主题的标题,链接和第一条评论,以 json 的格式。原文地址:[lesson4](https://github.com/alsotang/node-lessons/tree/master/lesson4)   

示例:
``` text
    [
      {
        "title": "【公告】发招聘帖的同学留意一下这里",
        "href": "http://cnodejs.org/topic/541ed2d05e28155f24676a12",
        "comment1": "呵呵呵呵"
      },
      {
        "title": "发布一款 Sublime Text 下的 JavaScript 语法高亮插件",
        "href": "http://cnodejs.org/topic/54207e2efffeb6de3d61f68f",
        "comment1": "沙发！"
      }
    ]
```

### 挑战

输出帖子的作者,以及他在cnode社区的积分值,这里和原文不同,作者看错了,不过也算挑战   
示例:

``` text
    [
      {
        "title": "【公告】发招聘帖的同学留意一下这里",
        "href": "http://cnodejs.org/topic/541ed2d05e28155f24676a12",
        "comment1": "呵呵呵呵",
        "author1": "auser",
        "score1": 80
      },
      ...
    ]
```

原文: 以上文目标为基础,输出 comment1 的作者,以及他在 cnode 社区的积分值。   
就一个地方不同,我过后会修改代码附上

<!-- more -->

首先app.js这样写,获取文章的url到数组:

``` code
    var eventproxy = require('eventproxy');
    var superagent = require('superagent');
    var cheerio = require('cheerio');
    // url 模块是 Node.js 标准库里面的
    // http://nodejs.org/api/url.html
    var url = require('url');
    var cnodeUrl = 'https://cnodejs.org/';
    superagent.get(cnodeUrl)
      .end(function (err, res) {
        if (err) {
          return console.error(err);
        }
        var topicUrls = [];
        var $ = cheerio.load(res.text);
        // 获取首页所有的链接
        $('#topic_list .topic_title').each(function (idx, element) {
          var $element = $(element);
          // $element.attr('href') 本来的样子是 /topic/542acd7d5d28233425538b04
          // 我们用 url.resolve 来自动推断出完整 url,变成
          // https://cnodejs.org/topic/542acd7d5d28233425538b04 的形式
          // 具体请看 http://nodejs.org/api/url.html#url_url_resolve_from_to 的示例
          var href = url.resolve(cnodeUrl, $element.attr('href'));
          topicUrls.push(href);
        });
        console.log(topicUrls);
      });
```

Copy:（这段是在太多,偷师学艺）   
用 js 写过异步的同学应该都知道,如果你要并发异步获取两三个地址的数据,并且要在获取到数据之后,对这些数据一起进行利用的话,常规的写法是自己维护一个计数器。

先定义一个 var count = 0,然后每次抓取成功以后,就 count++。如果你是要抓取三个源的数据,由于你根本不知道这些异步操作到底谁先完成,那么每次当抓取成功的时候,就判断一下 count === 3。当值为真时,使用另一个函数继续完成操作。

而 eventproxy 就起到了这个计数器的作用,它来帮你管理到底这些异步操作是否完成,完成之后,它会自动调用你提供的处理函数,并将抓取到的数据当参数传过来。

假设我们不使用 eventproxy 也不使用计数器时,抓取三个源的写法是这样的:

``` code
    // 参考 jquery 的 $.get 的方法
    $.get("http://data1_source", function (data1) {
      // something
      $.get("http://data2_source", function (data2) {
        // something
        $.get("http://data3_source", function (data3) {
          // something
          var html = fuck(data1, data2, data3);
          render(html);
        });
      });
    });
```

使用计数器:

``` code
    (function () {
      var count = 0;
      var result = {};
      $.get('http://data1_source', function (data) {
        result.data1 = data;
        count++;
        handle();
        });
      $.get('http://data2_source', function (data) {
        result.data2 = data;
        count++;
        handle();
        });
      $.get('http://data3_source', function (data) {
        result.data3 = data;
        count++;
        handle();
        });
      function handle() {
        if (count === 3) {
          var html = fuck(result.data1, result.data2, result.data3);
          render(html);
        }
      }
    })();
```

用 eventproxy,写出来是这样的:   

``` code
    var ep = new eventproxy();
    ep.all('data1_event', 'data2_event', 'data3_event', function (data1, data2, data3) {
      var html = fuck(data1, data2, data3);
      render(html);
    });
    $.get('http://data1_source', function (data) {
      ep.emit('data1_event', data);
      });
    $.get('http://data2_source', function (data) {
      ep.emit('data2_event', data);
      });
    $.get('http://data3_source', function (data) {
      ep.emit('data3_event', data);
      });
```

大家自行学习一下这个 API 吧:[eventproxy])https://github.com/JacksonTian/eventproxy#%E9%87%8D%E5%A4%8D%E5%BC%82%E6%AD%A5%E5%8D%8F%E4%BD%9C)

目标完成代码:   

``` code
    var eventproxy = require('eventproxy');
    var superagent = require('superagent');
    var cheerio    = require('cheerio');
    //加载标准库模块url
    var url = require('url');
    var cnodeUrl = 'https://cnodejs.org/';
    superagent.get(cnodeUrl)
        .end(function(err, res){
            if (err) {
                return console.error(err);
            };
            var $ = cheerio.load(res.text);
            var topicUrls = [];
            //获取链接
            $('#topic_list .topic_title').each(function(idx, element){
                var $element = $(element);
                var href = url.resolve(cnodeUrl, $element.attr('href'));
                topicUrls.push(href);
            });
            // console.log(topicUrls);
            // 创建对象实例
            var ep = new eventproxy();
            //监听topicUrls.length次'topic_html'再行动
            ep.after('topic_html', topicUrls.length, function(topics){
                //topic是个数组,包含了40次ep.emit('topic_html', pair)中的那40个pair
                topics = topics.map(function(topicPair){
                    var topicUrl = topicPair[0];
                    var topicHtml = topicPair[1];
                    var $ = cheerio.load(topicHtml);
                    return ({
                        title: $('.topic_full_title').text().trim(),
                        href: topicUrl,
                        comment: $('.reply_content').eq(0).text().trim()
                    });
                });
                console.log('final:');
                console.log(topics);
            });
            topicUrls.forEach(function(topicUrl){
                superagent.get(topicUrl)
                    .end(function(err, sres){
                        console.log('fetch: ' + topicUrl + ' successful');
                        ep.emit('topic_html', [topicUrl, sres.text]);
                    });
            });
        });
```

目标代码看似没问题,可是服务器异常(服务器受不了),我采用了递归完成了挑战,估计服务器更受不了,因为如果访问有错误,我就采用递归又去访问,直到40次全部完成

因为看错了,所以这是自己写的挑战自我的代码:

``` code
    var eventproxy = require('eventproxy');
    var superagent = require('superagent');
    var cheerio    = require('cheerio');
    //加载标准库模块url
    var url = require('url');
    var cnodeUrl = 'https://cnodejs.org/';
    superagent.get(cnodeUrl)
        .end(function(err, res){
            if (err) {
                return console.error(err);
            };
            var $ = cheerio.load(res.text);
            //一级,得到所有主题
            var topicUrls = [];
            //获取链接
            $('#topic_list .topic_title').each(function(idx, element){
                var $element = $(element);
                var href = url.resolve(cnodeUrl, $element.attr('href'));
                topicUrls.push(href);
            });
            // 创建对象实例
            var ep = new eventproxy();
            //监听topicUrls.length次'topic_html',再执行
            ep.after('topic_html', topicUrls.length, function(topics){
                //topic是个数组,包含了40次ep.emit('topic_html', pair)中的那40个pair
                // array.map方法会遍历数组并且每遍历一次执行一次callback
                //  callback执行后的返回值组合起来又形成一个新的数组
                /*
                topics = topics.map(function(topicPair){
                    var topicUrl = topicPair[0];
                    var topicHtml = topicPair[1];
                    console.log(topicHtml);
                    var $ = cheerio.load(topicHtml);
                    return ({
                        title: $('.topic_full_title').text().trim(),
                        href: topicUrl,
                        comment: $('.reply_content').eq(0).text().trim()
                    });
                });
                console.log('final:');
                console.log(topics);
                */
                ep.after('user', topics.length, function(users){
                    var finalInfors = users.map(function(user){
                        var preInfo = user[0];
                        var userHtml = user[1];
                        var $ = cheerio.load(userHtml);
                        var authorScore = $('.userinfo .user_profile .big').text();
                        return ({
                            title: preInfo[0],
                            href: preInfo[1],
                            comment: preInfo[2],
                            author: preInfo[3],
                            score: authorScore
                        });
                    });
                    console.log('final:');
                    console.log(finalInfors);
                });
                //这里如果有错误,就使用递归
                //请不要多次测试,服务器负载受不了
                //正式因为服务器负载问题,有时候访问会出错,所以使用了递归
                var getScore = function(userUrl, topicInfor){
                    superagent.get(userUrl)
                        .end(function(err, ssres){
                            if (err) {
                                getScore(userUrl, topicInfor);
                                return;
                            }
                            console.log('fetch suburl:' + userUrl + " success");
                            ep.emit(
                                'user', 
                                [
                                    topicInfor, 
                                    ssres.text
                                ]
                            );
                        });
                }
                topics.forEach(function(topic){
                    var topicUrl = topic[0];
                    var topicHtml = topic[1];
                    var $ = cheerio.load(topicHtml);
                    var $userInfo = $('.user_card .user_name a');
                    getScore(
                        url.resolve(cnodeUrl, $userInfo.attr('href')), 
                        [
                            $('.topic_full_title').text().trim(),
                            topicUrl,
                            $('.reply_content').eq(0).text().trim(),
                            $userInfo.text()
                        ]
                    );
                });
            });
            //这里如果有错误,就使用递归
            //请不要多次测试,服务器负载受不了
            //正式因为服务器负载问题,有时候访问会出错,所以使用了递归
            var getText = function(topicUrl){
                superagent.get(topicUrl)
                    .end(function(err, sres){
                        if (err) {
                            getText(topicUrl);
                            return;
                        }
                        console.log('fetch: ' + topicUrl + " success");
                        ep.emit(
                            'topic_html', 
                            [ 
                                topicUrl, 
                                sres.text
                            ]
                        );
                    });
            }
            topicUrls.forEach(function(topicUrl){
                getText(topicUrl);
            });
        });
```

原文中挑战目标的代码:   

``` code
    var eventproxy = require('eventproxy');
    var superagent = require('superagent');
    var cheerio    = require('cheerio');
    //加载标准库模块url
    var url = require('url');
    var cnodeUrl = 'https://cnodejs.org/';
    superagent.get(cnodeUrl)
        .end(function(err, res){
            if (err) {
                return console.error(err);
            };
            var $ = cheerio.load(res.text);
            //一级,得到所有主题
            var topicUrls = [];
            //获取链接
            $('#topic_list .topic_title').each(function(idx, element){
                var $element = $(element);
                var href = url.resolve(cnodeUrl, $element.attr('href'));
                topicUrls.push(href);
            });
            // 创建对象实例
            var ep = new eventproxy();
            ep.after('topic_html', topicUrls.length, function(topics){
                ep.after('user', topics.length, function(users){
                    var finalInfors = users.map(function(user){
                        var preInfo = user[0];
                        var userHtml = user[1];
                        var $ = cheerio.load(userHtml);
                        var authorScore = $('.userinfo .user_profile .big').text();
                        return ({
                            title: preInfo[0],
                            href: preInfo[1],
                            comment: preInfo[2],
                            author: preInfo[3],
                            score: authorScore
                        });
                    });
                    console.log('final:');
                    console.log(finalInfors);
                });
                //这里如果有错误,就使用递归
                //请不要多次测试,服务器负载受不了
                //正式因为服务器负载问题,有时候访问会出错,所以使用了递归
                var getScore = function(userUrl, topicInfor){
                    superagent.get(userUrl)
                        .end(function(err, ssres){
                            if (err) {
                                getScore(userUrl, topicInfor);
                                return;
                            }
                            console.log('fetch suburl:' + userUrl + " success");
                            ep.emit(
                                'user', 
                                [
                                    topicInfor, 
                                    ssres.text
                                ]
                            );
                        });
                }
                topics.forEach(function(topic){
                    var topicUrl = topic[0];
                    var topicHtml = topic[1];
                    var $ = cheerio.load(topicHtml);
                    var $authorInfor = $('.author_content').eq(0).find(".reply_author");
                    getScore(
                        url.resolve(cnodeUrl, $authorInfor.attr('href')), 
                        [
                            $('.topic_full_title').text().trim(),
                            topicUrl,
                            $('.reply_content').eq(0).text().trim(),
                            $authorInfor.text().trim()
                        ]
                    );
                });
            });
            //这里如果有错误,就使用递归
            //请不要多次测试,服务器负载受不了
            //正式因为服务器负载问题,有时候访问会出错,所以使用了递归
            var getText = function(topicUrl){
                superagent.get(topicUrl)
                    .end(function(err, sres){
                        if (err) {
                            getText(topicUrl);
                            return;
                        }
                        console.log('fetch: ' + topicUrl + " success");
                        ep.emit(
                            'topic_html', 
                            [ 
                                topicUrl, 
                                sres.text
                            ]
                        );
                    });
            }
            topicUrls.forEach(function(topicUrl){
                getText(topicUrl);
            });
        });
```

前两段代码亲测可用,第三段代码只是修改了获取作者积分的时候传递的url和作者的名称   
也许你会问为什么没有测试,因为社区发帖子的人很多啊,有一些帖子是没有人回复的,获取不到评论   
测试的时候还以为代码有错了,555