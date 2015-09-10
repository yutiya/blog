title: 无法编译AngulayJS源码
date: 2015-09-10 18:26:14
categories:
- AngularJS
- build
tags: AngularJS
---

## 无法编译AngulayJS源码

找到`./lib/versions/versions-info.js`文件并找到`getBuild()`函数并修改    

``` code
    function getBuild() {
      var hash = shell.exec('git rev-parse --short HEAD', {silent: true}).output.replace('\n', '');
      if (hash.code === 0) { // just check code answer as in other places
        return 'sha.'+hash;  
      }
      return '';
    }
```

安装`npm install -g grunt-cli`       

运行`grunt`自动编译

<!-- more -->

![](/images/angularjs/build/1.png)