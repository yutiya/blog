title: Nodejs第十二课
date: 2015-09-06 17:23:11
categories:
- nodejs
- lesson
tags: lesson
---

## 线上部署Node.js:heroku    

### 目标    

将[这个项目](https://github.com/Ricardo-Li/node-practice-2)部署上heroku,成为一个线上项目.    
参考原作者的App, http://serene-falls-9294.herokuapp.com/    

### 知识点    

- 学习heroku的线上部署:(https://www.heroku.com/)     

### 内容

使用`git clone git@github.com:Ricardo-Li/node-practice-2.git`命令克隆项目     
代码中的Procfile文件:    

``` text
    web: node app.js
```

一个是`app.js`文件:    

``` code
    app.listen(process.env.PORT || 5000);
```

这两者都是为了部署heroku所做.    

<!-- more -->

pass平台提供语言环境支持,对于`Node.js`来说,它要帮我们安装`package.json`中的依赖,然后启动我们的项目,并把外部流量导入我们的项目,让我们的项目提供服务.    
第一个是启动项目,第二个时引入外部流量      
我们提供Procfile知道heroku平台启动我们的项目    
我们的程序原本监听5000端口,但是heroku并不知道.可以在Procfile中指定,可以会有冲突,heroku使用了主动策略,提供了一个环境变量`process.evn.PORT`来供我们的App监听.    
首先下载工具包 https://toolbelt.heroku.com/ ,执行`heroku login`登录    
官方教程: https://devcenter.heroku.com/articles/getting-started-with-nodejs#introduction     
进入`node-practice-2`目录,执行`heroku create`,heroku会随机给我们分配应用名称和git仓库.     
是用`git remote -v`命令查看一下远程库的一些信息     

``` text
    heroku  https://git.heroku.com/arcane-coast-6683.git (fetch)
    heroku  https://git.heroku.com/arcane-coast-6683.git (push)
    origin  git@github.com:Ricardo-Li/node-practice-2.git (fetch)
    origin  git@github.com:Ricardo-Li/node-practice-2.git (push)
```

现在将我们的代码推送到heroku远程库`git push heroku master`     
heroku自动检测出我们是Node.js程序,帮我们安装依赖,然后按照Procfile启动.     
push完成后,按照官方教程`heroku ps:scale web=1`可以看到我们有一个App正在运行     
使用`heroku open`,自动打开浏览器访问.然后,就这么多了     

