---
title: 安卓sourceCompatibility = '1.7'系列解决思路
date: 2017-04-02 21:45:05
tags: ‘android’
---
问题描述：
windows平台 android studio 2.3
在android app 打包时出现系列异常
```
Error:Error converting bytecode to dex:
Cause: Dex cannot parse version 52 byte code.
This is caused by library dependencies that have been compiled using Java 8 or above.
If you are using the 'java' gradle plugin in a library submodule add
targetCompatibility = '1.7'
sourceCompatibility = '1.7'
to that submodule's build.gradle file.
```

查明原因：
=================
由于我将其它同事基于jdk8编写的源代码作为模块添加进我的项目里，而在他的模块中有类似语句
![](http://i2.muimg.com/4851/752f799d38855519.png)
这里虽然warning level是Error的，但是依然能够编译。  
在与同事协商后，打算这段代码保留不动，动我前端的配置。  
方案一  
--------------
假设我项目里包含了这段代码的模块名叫module_A，则在module_A对应的build.gradle里面添加
```
targetCompatibility = '1.7'
sourceCompatibility = '1.7'
```
但这个方案不是特别稳定，还有待读者测试

类似的[解决方案](http://www.cnblogs.com/helloshrek/p/6018547.html)
这个方案我准备用LocalDate测试一下

方案二：一个耗时的方法
------------

在安卓中使用一些java 8特性的办法就是在你的 app模块里加入
```
android{
defaultConfig{...
  jackOptions{enabled true}
}}
```

缺点
------------
可看看这个
![](http://i4.buimg.com/4851/e76bd5c07b0da1fd.png)
这就叫做Instant Run与java 8不能兼得啊

开了jackOptions之后，Instant Run就被关闭了，意味着，每次对代码的小改动，整个apk都需要重头重新打包，很可能每次run on device的耗时就是几分钟以上了

此外，这个属性在android库模块里是默认关闭且不能设置为true的，否则会在gradle sync的时候出现错误：  
```Error:Library projects cannot enable Jack. Jack is enabled in default config.```


最后要特别提醒的是 在windows平台上，android默认的内嵌jre可不是Oracle jdk8的，而是自己魔改的open jdk8
当前项目使用的路径可以在Project Structure -> SDK Location标签页里面看到
