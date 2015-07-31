title: nodejs lesson1
date: 2015-07-28 17:06:13
categories:
- nodejs
- lesson
tags: lesson
---

## nodejs lesson1

Mac下:
`sudo npm install -g express-generator@3` (express3)
`sudo npm install -g express-generator` (express4)

下面的学习均按照express3进行   

### 创建express项目:   
``` bash
$ express -e nodejs-demo
$ cd nodejs-demo
$ npm install
$ vi app.js
```

app.js:

``` text
var express = require('express');
var app = express();
app.get('/', function(req, res){
    res.send('Hello World');
});
app.listen(3000, function(){
    console.log('app is listening at port 3000');
});
```

执行`$ node app.js`
访问`localhost:3000`,可以看到打印的Hello World!.

### URL

我们熟悉的url其实是这个样子:
`<scheme>://<user>:<password>@<host>:<port>/<url-path>`   

ftp:
`ftp://账号:密码@主机/目录或文件`   

现在的使用的url精简成了这样:
`<scheme>://<host>:<port>/<url-path>`