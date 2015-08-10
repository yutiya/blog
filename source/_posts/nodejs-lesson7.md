title: Nodejs第七课
date: 2015-08-10 18:40:11
categories:
- nodejs
- lesson
tags: lesson
---

## 浏览器端测试: mocha、chai、phantomjs

### 目标

- 建立lesson7项目,编写代码
- main.js类似第六节课中提交的fibonacci函数
- 此函数的定义为`int fibonacci(int n)`
    - 当`n === 0`,返回0.`n === 1`时,返回1
    - `n > 1`,返回`fibonacci(n) === fibonacci(n-1) + fibonacci(n-2)`,如`fibonacci(10) === 55`
- verdor文件:前端单元测试环境
- verdor/test.js编写针对前端脚本的测试用例

### 知识点

- 学习使用[测试框架mocha进行前端测试](http://mochajs.org/)
- 了解[全栈的断言库chai](http://chaijs.com/)
- 了解[headless浏览器phantomjs](http://phantomjs.org)

### 前端脚本单元测试

lesson6的内容都是针对后端环境中node的一些单元测试方案,出于应用健壮性的考量,针对前端js脚本的单元测试也非常重要.而前后端通吃,也是mocha的一大特点.    
首先,前端脚本的单元测试主要由两个困难需要解决.
- 运行环境在浏览器中,可以操作浏览器的DOM对象,且可以随意定义执行时的HTML上下文
- 测试结果应当可以直接反馈给mocha,判断测试是否可以通过


<!-- more -->

### 浏览器环境下执行

首先搭建测试原型,执行`mocha init f2e`

mocha就会自动生成一个简单的测试原型   

``` text
// f2e
    .
    ├── index.html
    ├── mocha.css
    ├── mocha.js
    └── tests.js
```

其中index.html是单元测试的入口,test.js是我们的测试用例文件    
我们直接在index.html插入上述示例的fibonacci函数以及断言库chaijs 

chai模块可以随意建立一个空项目然后安装后复制使用   

``` code
    $ npm init
    $ npm install chaijs --save
```

在node_modules文件夹中复制chai文件夹到f2e文件夹中

``` text
// f2e
    .
    ├── index.html
    ├── mocha.css
    ├── mocha.js
    ├── tests.js
    └──chai
        ──...(chai模块的所有文件)
```

index.html:    

``` code
    <div id="mocha"></div>
    <script src="mocha.js"></script>
    <script src="chai/chai.js"></script>
    <script>
      var fibonacci = function(n) {
        if (n === 0) {
          return 0;
        }
        if (n === 1) {
          return 1;
        }
        return fibonacci(n-1) + fibonacci(n-2);
      };
    </script>
    <script>mocha.setup('bdd')</script>
    <script src="tests.js"></script>
    <script>
      mocha.run();
    </script>
```

test.js中写入对应的测试用例

``` code
    var should = chai.should();
    describe('simple test', function(){
        it('should equal 0 when n === 0', function(){
            window.fibonacci(0).should.equal(0);
        });
    });
```

使用浏览器打开index.html,完成测试    

![](/images/nodejs/lesson7/1.png)

### 测试反馈

mocha没有提供一个命令行的前端脚本测试环境(因为我们的脚本文件需要运行在浏览器环境中),因为我们使用phanatomjs帮助我们搭建一个模拟环境.不重复造轮子,直接使用mocha-phantomjs帮助我们在命令行运行测试    

安装` $ sudo npm install -g mocha-phantomjs`    
对应修改index.html:

``` code
    <script>
      if(window.mochaPhantomJS) { mochaPhantomJS.run(); }
      else{ mocha.run(); }
    </script>
```

运行` $ mocha-phantomjs index.html`查看测试结果

![](/images/nodejs/lesson7/2.png)

也可以使用另一种方案,在package.json中定义    
``` code
    "script": {
        "test": "./node_modules/.bin/mocha-phantomjs index.html"
    }
}
```

将mocha-phantomjs作为依赖
``` code
    $ npm init
    $ npm install mocha-phantomjs --save-dev
```

直接运行` $ npm test`    

![](/images/nodejs/lesson7/3.png)

至此,我们实现了前端脚本的单元测试，基于 phanatomjs 你几乎可以调用所有的浏览器方法，而 mocha-phanatomjs 也可以很便捷地将测试结果反馈到 mocha，便于后续的持续集成。
