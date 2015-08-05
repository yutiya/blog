title: Nodejs第六课
date: 2015-08-05 16:45:10
categories:
- nodejs
- lesson
tags: lesson
---

## 测试用例: mocha, should, istanbul

第六课, 原文链接[Node.js-lesson6](https://github.com/alsotang/node-lessons/tree/master/lesson6)

### 目标   
建立lesson6项目,编写代码   
main.js: 其中有个fibonacci函数.fibonacci的介绍详见[维基百科](http://en.wikipedia.org/wiki/Fibonacci_number ),中文名 斐波那契   
此函数的定义为` int fibonacci(int n) `   

- 当 n === 0 时,返回0; n === 1 时,返回1
- n > 1 时,返回` fibonacci(n) = fibonacci(n - 1) + fibonacci(n-2) `,如` fibonacci === 55 `    
- n不能大于10,否则抛错,因为Node.js的计算性能没那么强,到此为止
- n也不能小于0,否则抛错,因为没意义
- n不为数字时,抛错

test/main.test.js: 对main函数进行测试,并使行覆盖率和分支覆盖率都达到100%    

知识点：
- 学习使用测试框架 [mocha](http://mochajs.org/)
- 学习使用断言库 [should](https://github.com/tj/should.js)
- 学习使用测试率覆盖工具 [istanbul](https://github.com/gotwarlost/istanbul)
- 简单[Makefile的编写](http://blog.csdn.net/haoel/article/details/2886)

### 课程内容

` $ npm init ` 初始化项目    
编辑 main.js,编写 `fibonacci` 函数   

``` code
    // file main.js
    var fibonacci = function(n) {
        if(n === 0) {
            return 0;
        }
        if(n === 1) {
            return 1;
        }
        return fibonacci(n-1) + fibonacci(n-2);
    };
    if(require.main === module) {
        //  如果是直接执行main.js,则进入此处
        //  如果是main.js被其他文件require,则此处不会执行
        var n = Number(process.argv[2]);
        console.log('fibonacci(' + n + ' ), is', fibonacci(n));
    }
```

执行 ` $ node main.js 10`运行

![](/images/nodejs/lesson6/1.png)

<!-- more -->

接下来开始测试驱动开发,我们先得把main.js里面的fibonacci暴露出来,这个简单   
加上` exports.fibonacci = fobonacci;`(我承认,我看不懂,我还需要补充基础知识)    

然后在`test/main.test.js`中引用我们的main.js,开始一个简单的测试

``` code
    // file:  test/main.test.js
    var main = require('../main');
    var should = require('should');
    describe('test/main.test.js', function () {
        it('should equal 55 when n === 10', function(){
            main.fibonacci(10).should.equal(55);
        });
    });
```

测试之前,需要使用`npm install should --save`安装should这个外部模块    
然后安装一个全局的mocha:` $ sudo npm install mocha -g`    
执行:` $ mocha`   
![](/images/nodejs/lesson6/2.png)

should模块是一个断言库,mocha和should协作完成测试    
should在Javascript的Object'基类'上注入了一个`#should`属性,这个属性又带有许许多多的属性可以访问    
比如测试一个数是不是大于3,则是`(5).should.above(3)`,测试一个字符串是否有着特定的前缀:`'foobar'.should.startWith('foo');`,[should.js API](https://github.com/tj/should.js)   
should.js 如果现在还是 version 3 的话,推荐大家去看看它的 API 和 源码;现在 should 是 version 4 了,API 丑得很,但为了不掉队,我还是一直用着它.我觉得 expect 麻烦,所以不用 expect,对了,expect 也是一个断言库:https://github.com/LearnBoost/expect.js/.   

记得fibonacci函数的几个要求吗？

``` text
* 当 n === 0 时,返回0; n === 1 时,返回1
* n > 1 时,返回` fibonacci(n) = fibonacci(n - 1) + fibonacci(n-2) `,如` fibonacci === 55 `    
* n不能大于10,否则抛错,因为Node.js的计算性能没那么强,到此为止
* n也不能小于0,否则抛错,因为没意义
* n不为数字时,抛错
```

现在就让我们学习使用测试用例来描述一下这几个要求,更新后的main.test.js:

``` code
    // file test/main.test.js
    var main = require('../main');
    var should = require('should');

    describe('test/main.test.js', function () {

        it('should equal 0 when n === 0', function(){
            main.fibonacci(0).should.equal(0);
        });

        it('should equal 1 when n === 1', function(){
            main.fibonacci(1).should.equal(1);
        });

        it('should equal 55 when n === 10', function(){
            main.fibonacci(10).should.equal(55);
        });

        it('should throw when n > 10', function () {
            (function(){
                main.fibonacci(11);
            }).should.throw('n should <= 10');
        });

        it('should throw when n < 0', function(){
            (function () {
                main.fibonacci(-1);
            }).should.throw('n should >= 0');
        });

        it('should throw when n isn\'t Number', function(){
            (function(){
                main.fibonacci('呵呵');
            }).should.throw('n should be a Number');
        });
    });
```

执行` $ mocha`,上图,没通过:
![](/images/nodejs/lesson6/3.png)

现在我们只能修改fibonacci的实现了:

``` code
    // file main.js
    var fibonacci = function(n) {
        if(typeof n !== 'number') {
            throw new Error('n should be a Number');
        }
        if(n < 0) {
            throw new Error('n should >= 0');
        }
        if(n > 10) {
            throw new Error('n should <= 10');
        }
        if(n === 0) {
            return 0;
        }
        if(n === 1) {
            return 1;
        }
        return fibonacci(n-1) + fibonacci(n-2);
    };

    if(require.main === module) {
        //  如果是直接执行main.js,则进入此处
        //  如果是main.js被其他文件require,则此处不会执行
        var n = Number(process.argv[2]);
        console.log('fibonacci(' + n + ' ), is', fibonacci(n));
    }

    exports.fibonacci = fibonacci;
```

再次执行` $ mocha`,就过了.我也是现在才知道这就是传说中的测试驱动开发:    
先把要达到的目的都描述清楚,然后让现有的程序跑不过case,再修补,让case通过.    
安装一个istanbul:`$ sudo npm install istanbul -g`    
执行` $ istanbul cover _mocha`    
会看到比直接使用mocha命令多一些输出内容,而且很生成一些文件   
![](/images/nodejs/lesson6/4.png)

Branches:分支覆盖率  Statements:行覆盖率    
使用命令打开` $ open coverage/lcov-report/index.html`   
![](/images/nodejs/lesson6/5.png)

其实测试的覆盖率是100%的，但是23、24这两行无法测试    
mocha和istanbul是无缝结合的,只要能使用mocha测试,那么就能配合istanbul测试    
剩下的就是需要说明版本兼容问题,程序都是向下兼容,低版本肯定跑不起高版本的一些东西    


假设你有一个项目A,用到了 mocha 的 version 3,其他人有个项目B,用到了 mocha 的 version 10,那么如果你 ` $ sudo npm install mocha -g` 装的是 version 3 的话,你用 $ mocha 是不兼容B项目的.因为 mocha 版本改变之后,很可能语法也变了,对吧.

这时，跑测试用例的正确方法，应该是:    
- ` $ npm install mocha --save-dev`,装个 mocha 到项目目录中去
- ` $ ./node_modules/.bin/mocha`,用刚才安装的这个特定版本的 mocha,来跑项目的测试代码.

./node_modules/.bin 这个目录下放着我们所有依赖自带的那些可执行文件(该目录为隐藏目录` $ ls -a`可查看)    

每次输入这个很麻烦对吧?所以我们要引入 Makefile,让 Makefile 帮我们记住复杂的配置.

``` text
    test:
        ./node_modules/.bin/mocha

    cov test-cov:
        ./node_modules/bin/istanbul cover _mocha

    .PHONY: test cov test-cov
```

这个时候,只需要调用` $ make test`或者` $ make cov`,就可以测试了

Makefile学习链接: http://blog.csdn.net/haoel/article/details/2886 