title: 迈向世界的第一步
date: 2015-07-23 16:00:00
categories: [Hexo, Use]
tags: 第一次使用Hexo
---

# 迈向世界的第一步

 使用[Hexo](http://hexo.io/)! 非常棒. 
 查看 [documentation](http://hexo.io/docs/) 获取更多信息.
 使用过程中遇到问题，请查看[troubleshooting](http://hexo.io/docs/troubleshooting.html) 得到帮助或者上[GitHub](https://github.com/hexojs/hexo/issues)提问.

## 开始吧
 - 安装: npm install hexo-cli -g
 - 初始化: hexo init <floder>
 - 安装组件: npm install (自动完成)
 - 安装主题: git clone <theme-url> themes/<folder>
 - [配置](https://hexo.io/zh-cn/docs/configuration.html): _config.yml
 - 生成: hexo g
 - 本地部署: hexo s

<!-- more -->

## 新的篇章

``` bash
    $ hexo new "新的开始"
```

更多: [Writing](http://hexo.io/docs/writing.html)

``` bash
    $ hexo deploy
```

更多: [Deployment](http://hexo.io/docs/deployment.html)

### 文章配置

| title: 标题
| date: 日期
| categories: 分类
``` bash
    可以输入多个，顺序性 
    ` categories: [Hexo, Use] `
    或者
    `
    categories:
        - Hexo
        - Use
    `
```
| tags: 标记

