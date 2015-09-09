title: 百度地图SDK之引擎初始化失败
date: 2015-09-09 12:50:52
categories: 
- 百度地图SDK
- bug
tags: 百度地图
---

## 百度地图引擎初始化失败

百度地图升级了SDK,库文件编程了framework.可以看到Resources文件夹下有mapppi.bundle.    

将framework拖入工程,你会发现工程能运行,地图也能出来,可是突然某一天你发现地图就不显示了.    
在官方文档中讲述了需要引入mapapi.bundle,那为何将该文件打包至framework中?你能想象,之前能运行,结果后来一直报内存泄露的错误,最终只是因为这个bundle文件没有加入工程吗?    

引以为戒