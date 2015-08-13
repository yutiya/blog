title: Nodejs第八课
date: 2015-08-13 18:20:25
categories:
- nodejs
- lesson
tags: lesson
---

## 测试用例:supertest

学习中,[原文链接](https://github.com/alsotang/node-lessons/tree/master/lesson8)

### 目标

建立一个lesson8项目,在其中编写代码     

app.js:其中有个fibonacci接口

fibonacci函数的定义为`int fibonacci(int n)`,调用函数的url路径是`/fib?n=10`,然后这个接口会返回55    
函数的行为定义如下:    

- 当`n === 0`时,返回0;`n === 1`时，返回1
- `n > 1`时,返回`fibonacci(n) === fibonacci(n-1) + fibonacci(n-2)`,如`fibonacci(10) === 55`
- n不可大于10,否则抛错,`http status 500`,因为Node.js的计算性能没那么强。
- n也不可小于0,否则抛错500,因为没意义。
- n不为数字时，抛错,500

test/app.test.js:对app的接口进行测试,覆盖以上所有情况

### 知识点

- 学习[supertest](https://github.com/tj/supertest )的使用
- 复习mocha,should的使用

### 开始

``` bash
    $ npm init # 来吧,一阵阵的键盘声(不停的回车)
```

<!-- more -->

然后编写安装模块    
` $ npm install xxx --save`和` $ npm install xxx --save-dev`是有差异的噢,修改于配置文件中的地方不同噢

``` config
    "devDependencies": {
        "mocha": "^1.21.4",
        "should": "^4.0.4",
        "supertest": "^0.14.0"
    },
    "dependencies": {
        "express": "^4.9.6"
    }
```

编写代码app.js:

``` code
    var express = require('express');

    var fibonacci = function(n) {
        if (typeof n !== 'number' || isNaN(n)) {
            throw new Error('n should be a Number');
        }
        if (n < 0) {
            throw new Error('n should >= 0');
        }
        if (n > 10) {
            throw new Error('n should <= 10');
        }
        if (n === 0) {
            return 0;
        }
        if (n === 1) {
            return 1;
        }
        return fibonacci(n-1) + fibonacci(n-2);
    };


    var app = express();

    app.get('/fib', function(req, res){
        var n = Number(req.query.n);
        try{
            //如果直接给个数字的话,它会当成你给了一个http状态码,所以我们转换成String
            res.send(String(fibonacci(n)));
        } catch(e) {
            res.status(500)
                .send(e.message);
        }
    });
    // module.exports与exports的区别
    module.exports = app;

    app.listen(3000, function(){
        console.log('app ls listening at port 3000');
    });

```

运行` $ node app.js`

访问`http://localhost:3000/fib?n=10`,看到55就成功了.    
访问`http://localhost:3000/fib?n=111`,会看到`n should <= 10`.    

大家可以去安装一个[nodemon](https://github.com/remy/nodemon)    
` $ npm install -g nodemon`    
这个库是专门调试的时候使用,会自动检测Node.js代码的改动,然后帮你自动重启应用    
在调试时可以完全用nodemon命令代替node命令    
使用` $ nodemon app.js`启动我们的应用试试,然后添加两行输出代码,应用自动重启了    

现在我们继续写完测试代码,`test/app.test.js`:    

``` code
    var app = require('../app');
    var supertest = require('supertest');
    //关键代码,下面的request对象可以直接按照
    //superagent的api进行调用
    var request = supertest(app);

    var should = require('should');

    describe('test/app.test.js', function(){
        it('should return 55 when n is 10', function(done){
            //function之所以要接受一个done函数,是因为我们的测试内容涉及了异步调用
            //而mocha是无法感知异步调用完成的.所以主动接受它提供的done函数
            //在测试完毕时,自行调用一下,表示结束
            //mocha可以感知到我们的测试函数是否接受done参数.
            //js中,function对象是有长度的,长度由它的参数数量决定
            //(function(a, b, c, d){}).length === 4
            //所以mocha测试了函数的长度就可以确定我们是否是异步测试

            request.get('/fib')
                //query方法用来传querystring
                //send方法用来传body
                //下面的query等于访问 /fib?n=10
                .query({n: 10})
                .end(function(err, res){
                    //http返回的是string,所以传入字符串'55'
                    res.text.should.equal('55');
                    done(err);
                    // done(err)用法很鸡肋
                    // 完整写法
                    /*
                    should.not.exist(err);
                    res.text.should.equal('55');
                    */
                });
        });

        //由于代码基本相似,我们抽象一个testFib方法
        var testFib = function(n, statusCode, expect, done) {
            request.get('/fib')
                .query({n: n})
                .expect(statusCode)
                .end(function(err, res){
                    res.text.should.equal(expect);
                    done(err);
                });
        };

        it('should return 0 when n === 0', function(done) {
            testFib(0, 200, '0', done);
        });
        it('should return 1 when n === 1', function(done) {
            testFib(1, 200, '1', done);
        });
        it('should equal 55 when n === 10', function(done) {
            testFib(10, 200, '55', done);
        });
        it('should throw error when n > 10', function(done) {
            testFib(11, 500, 'n should <= 10', done);
        });
        it('should throw error when n < 0', function(done) {
            testFib(-1, 500, 'n should >= 0', done);
        });

        //单独测试一下返回码500
        it('should status 500 when error', function(done) {
            request.get('/fib')
                .query({n: 100})
                .expect(500)
                .end(function(err, res) {
                    done(err);
                });
        });
    });
```

执行` $ mocha`,得到结果    

![](/images/nodejs/lesson8/1.png)

### 关于cookie持久化

- 1.在supertest中,通过`var agent = supertest.agent(app)`获取一个agent对象,这个对象的API跟直接在superagent上调用各种方法是一样的.agent对象在被多次调用get和post之后,可以一路把cookie都保存下来    

``` code
    var supertest = require('supertest');
    var app = express();
    var agent = supertest.agent(app);

    agent.post('login').end(...);
    // do somethings ...
    agent.post('create_topic').end(...); // 此时的agent中有用户登录后的cookie
```

- 2.在发起请求时,调用`.set('Cookie', 'a cookie string')`这样的方式.

``` code
    var supertest = require('supertest');
    var userCookie;
    supertest.post('login').end(function(err, res) {
        userCookie = res.headers['Cookie'];
    });
    supertest.post('create_topic')
        .set('Cookie', userCookie)
        .end(...);
```

这里边有非常详细的介绍,可以学习:    
https://github.com/cnodejs/nodeclub/blob/master/test/controllers/topic.test.js








