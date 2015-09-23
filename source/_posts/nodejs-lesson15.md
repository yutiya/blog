title: Nodejs第十五课
date: 2015-09-23 15:20:12
categories:
- nodejs
- lesson
tags: lesson
---

## mongdb和mongoose使用简介

### 安装

mac:    

`brew update`    需要先安装Homebrew.更新
`brew install mongdb`    安装
`sudo mongod -—config /usr/local/etc/mongod.conf`   启动

<!-- more -->

### 简短实例

```
// 引入mongoose模块
var mongoose = require('mongoose');
// 连接对应的数据库
// mongodb是协议名称,localhost   是ip地址
// 端口号默认27017,test为数据库名称
// mongodb中不需要建立数据库,当连接是如果数据库不存在,会自动创建
// mongodb的安全性很残废,不过不用担心,反正也只能本机连接
mongoose.connect('mongodb://localhost/test');
// 创建一个名为Cat的model,它在数据库中的名字根据传给mongoose.model的第一个参数决定
// mongoose会将名词变为附属,在这里,collection的名字会是'cats'
// 这个model中定义了,String类型的name,String数组类型的friends,Number类型的age
// mongodb中大多数的数据类型都可以用js的原生类型来表示.至于说String的长度是多少,Number的精度是多少.String的最大限度是16MB,Number的整型是64-bit,浮点数的话,js中`0.1 + 0.2`的结果都是乱来的..就不指望什么了..
// 这里可以看到各种示例: http://mongoosejs.com/docs/schematypes.html
var Cat = mongoose.model('Cat', {
    name: String,
    friends: [String],
    age: Number,
});
// new一个新对象,kitty,接着赋值
var kitty = new Cat({ name: 'kitty', friends: ['tom', 'jerry']});
kitty.age = 3;
//调用save方法,mongoose会去的mongodb中的test数据库,存入一条数据
kitty.save(function(err){
    if(err) {
        console.log('gg');
    }
});
```

使用`mongo`连接查看    

```
show dbs //显示所有数据库
use test //切换数据库
show collections 
db.cats.find()
```