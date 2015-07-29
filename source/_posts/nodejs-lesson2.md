title: nodejs lesson2
date: 2015-07-29 18:42:22
categories:
- nodejs
- express
tags: express
---

## 使用外部模块

### 目标
建立一个新的项目，实现在浏览器中访问`http://localhost:3000/?q=yutiya`时   
输出yutiya的md5值

### 简介 package.json
该文件包含了项目的依赖等各种信息,当在项目文件夹下执行`npm install`会自动读取该文件,安装依赖到node_modules文件夹下,最后运行就可以跑起来了

``` bash
$ mkdir lesson2 && cd lesson2
$ npm init
```

终端会提示输入信息,可以全部回车,然后使用编辑器打开,就能看到对应的键值了   
init的目的就是为了生成package.json

执行` $ npm install express utility --save`   
会自动在json文件中添加依赖项,类似下面这样

``` text
  "dependencies": {
    "express": "^4.13.1",
    "utility": "^1.4.0"
  }
```

`node_modules`也会有对应的文件内容产生噢

<!-- more -->

编写代码,文件(app.js):

``` code
    //引入依赖
    var express = require('express');
    var utility = require('utility');
    //建立实例
    var app = express();
    app.get('/', function(req, res){
        //取值
        var q = req.query.q;
        //使用utility模块计算md5
        var md5Value = utility.md5(q);
        res.send(md5Value);
    });
    app.listen(3000, function(req, res){
        console.log('app is running at port 3000');
    });
```

运行` $ node app.js `,访问`http://localhost:3000/`,报错

``` text
TypeError: Not a string or buffer
   at TypeError (native)
   at Hash.update (crypto.js:119:16)
   at Object.hash (/Users/admin/Desktop/lesson2/node_modules/utility/lib/crypto.js:31:7)
   at Object.md5 (/Users/admin/Desktop/lesson2/node_modules/utility/lib/crypto.js:44:18)
   at /Users/admin/Desktop/lesson2/app.js:9:25
   at Layer.handle [as handle_request] (/Users/admin/Desktop/lesson2/node_modules/express/lib/router/layer.js:95:5)
   at next (/Users/admin/Desktop/lesson2/node_modules/express/lib/router/route.js:131:13)
   at Route.dispatch (/Users/admin/Desktop/lesson2/node_modules/express/lib/router/route.js:112:3)
   at Layer.handle [as handle_request] (/Users/admin/Desktop/lesson2/node_modules/express/lib/router/layer.js:95:5)
   at /Users/admin/Desktop/lesson2/node_modules/express/lib/router/index.js:277:22
```

可以看到，这个错误是从 `crypto.js` 中抛出的。

这是因为，当我们不传入 `q` 参数时，`req.query.q` 取到的值是 `undefined`，`utility.md5` 直接使用了这个空值，导致下层的 `crypto` 抛错。

访问`http://localhost:3000/?q=yutiya`,显示`4d493e09017ac232455bca190f9eb2e8`

查看[utility文档](https://github.com/node-modules/utility),修改成显示yutiya的sha1值

``` code
  var md5Value = utility.md5(q);
          res.send(md5Value);
```

打印结果`7fadc38a6aecdf260414e2b3752f81c954d56d3b`
