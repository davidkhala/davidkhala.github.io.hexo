---
title: 安卓sourceCompatibility = '1.7'系列解决思路
date: 2017-04-02 21:45:05
tags:
- 排雷
- Android
- ASTRI
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
<!--more-->

在gradle console里面能看到连串的
```
AGPBI: {"kind":"error","text":"Error converting bytecode to dex:\nCause: Dex cannot parse version 52 byte code.\nThis is caused by library dependencies that have been compiled using Java 8 or above.\nIf you are using the \u0027java\u0027 gradle plugin in a library submodule add \ntargetCompatibility \u003d \u00271.7\u0027\nsourceCompatibility \u003d \u00271.7\u0027\nto that submodule\u0027s build.gradle file.","sources":[{}],"original":"UNEXPECTED TOP-LEVEL EXCEPTION:\njava.lang.RuntimeException: Exception parsing classes\n\tat com.android.dx.command.dexer.Main.processClass(Main.java:781)\n\tat com.android.dx.command.dexer.Main.processFileBytes(Main.java:747)\n\tat com.android.dx.command.dexer.Main.access$1200(Main.java:88)\n\tat com.android.dx.command.dexer.Main$FileBytesConsumer.processFileBytes(Main.java:1689)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:170)\n\tat com.android.dx.cf.direct.ClassPathOpener.processDirectory(ClassPathOpener.java:229)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:158)\n\tat com.android.dx.cf.direct.ClassPathOpener.processDirectory(ClassPathOpener.java:229)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:158)\n\tat com.android.dx.cf.direct.ClassPathOpener.processDirectory(ClassPathOpener.java:229)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:158)\n\tat com.android.dx.cf.direct.ClassPathOpener.processDirectory(ClassPathOpener.java:229)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:158)\n\tat com.android.dx.cf.direct.ClassPathOpener.processDirectory(ClassPathOpener.java:229)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:158)\n\tat com.android.dx.cf.direct.ClassPathOpener.process(ClassPathOpener.java:144)\n\tat com.android.dx.command.dexer.Main.processOne(Main.java:695)\n\tat com.android.dx.command.dexer.Main.processAllFiles(Main.java:592)\n\tat com.android.dx.command.dexer.Main.runMultiDex(Main.java:376)\n\tat com.android.dx.command.dexer.Main.run(Main.java:290)\n\tat com.android.builder.internal.compiler.DexWrapper.run(DexWrapper.java:54)\n\tat com.android.builder.core.DexByteCodeConverter.lambda$dexInProcess$0(DexByteCodeConverter.java:174)\n\tat java.util.concurrent.FutureTask.run(FutureTask.java:266)\n\tat java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)\n\tat java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)\n\tat java.lang.Thread.run(Thread.java:745)\nCaused by: com.android.dx.cf.iface.ParseException: bad class file magic (cafebabe) or version (0034.0000)\n\tat com.android.dx.cf.direct.DirectClassFile.parse0(DirectClassFile.java:476)\n\tat com.android.dx.cf.direct.DirectClassFile.parse(DirectClassFile.java:406)\n\tat com.android.dx.cf.direct.DirectClassFile.parseToInterfacesIfNecessary(DirectClassFile.java:388)\n\tat com.android.dx.cf.direct.DirectClassFile.getMagic(DirectClassFile.java:251)\n\tat com.android.dx.command.dexer.Main.parseClass(Main.java:793)\n\tat com.android.dx.command.dexer.Main.access$1600(Main.java:88)\n\tat com.android.dx.command.dexer.Main$ClassParserTask.call(Main.java:1728)\n\tat com.android.dx.command.dexer.Main.processClass(Main.java:779)\n\t... 25 more\n","tool":"Dex"}
```
以及

```
AGPBI: {"kind":"error","text":"Error converting bytecode to dex:\nCause: Dex cannot parse version 52 byte code.\nThis is caused by library dependencies that have been compiled using Java 8 or above.\nIf you are using the \u0027java\u0027 gradle plugin in a library submodule add \ntargetCompatibility \u003d \u00271.7\u0027\nsourceCompatibility \u003d \u00271.7\u0027\nto that submodule\u0027s build.gradle file.","sources":[{}],"original":"UNEXPECTED TOP-LEVEL EXCEPTION:\njava.lang.RuntimeException: Exception parsing classes\n\tat com.android.dx.command.dexer.Main.processClass(Main.java:781)\n\tat com.android.dx.command.dexer.Main.processFileBytes(Main.java:747)\n\tat com.android.dx.command.dexer.Main.access$1200(Main.java:88)\n\tat com.android.dx.command.dexer.Main$FileBytesConsumer.processFileBytes(Main.java:1689)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:170)\n\tat com.android.dx.cf.direct.ClassPathOpener.processDirectory(ClassPathOpener.java:229)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:158)\n\tat com.android.dx.cf.direct.ClassPathOpener.processDirectory(ClassPathOpener.java:229)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:158)\n\tat com.android.dx.cf.direct.ClassPathOpener.processDirectory(ClassPathOpener.java:229)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:158)\n\tat com.android.dx.cf.direct.ClassPathOpener.processDirectory(ClassPathOpener.java:229)\n\tat com.android.dx.cf.direct.ClassPathOpener.processOne(ClassPathOpener.java:158)\n\tat com.android.dx.cf.direct.ClassPathOpener.process(ClassPathOpener.java:144)\n\tat com.android.dx.command.dexer.Main.processOne(Main.java:695)\n\tat com.android.dx.command.dexer.Main.processAllFiles(Main.java:592)\n\tat com.android.dx.command.dexer.Main.runMultiDex(Main.java:376)\n\tat com.android.dx.command.dexer.Main.run(Main.java:290)\n\tat com.android.builder.internal.compiler.DexWrapper.run(DexWrapper.java:54)\n\tat com.android.builder.core.DexByteCodeConverter.lambda$dexInProcess$0(DexByteCodeConverter.java:174)\n\tat java.util.concurrent.FutureTask.run(FutureTask.java:266)\n\tat java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)\n\tat java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)\n\tat java.lang.Thread.run(Thread.java:745)\nCaused by: com.android.dx.cf.iface.ParseException: bad class file magic (cafebabe) or version (0034.0000)\n\tat com.android.dx.cf.direct.DirectClassFile.parse0(DirectClassFile.java:476)\n\tat com.android.dx.cf.direct.DirectClassFile.parse(DirectClassFile.java:406)\n\tat com.android.dx.cf.direct.DirectClassFile.parseToInterfacesIfNecessary(DirectClassFile.java:388)\n\tat com.android.dx.cf.direct.DirectClassFile.getMagic(DirectClassFile.java:251)\n\tat com.android.dx.command.dexer.Main.parseClass(Main.java:793)\n\tat com.android.dx.command.dexer.Main.access$1600(Main.java:88)\n\tat com.android.dx.command.dexer.Main$ClassParserTask.call(Main.java:1728)\n\tat com.android.dx.command.dexer.Main.processClass(Main.java:779)\n\t... 23 more\n","tool":"Dex"}
```

查明原因
=================
由于我将其它同事基于jdk8编写的源代码作为模块添加进我的项目里，而在他的模块中有类似语句
![](http://i2.muimg.com/4851/752f799d38855519.png)
这里虽然warning level是Error的，但是依然能够编译。  
在与同事协商后，打算这段代码保留不动，动我前端的配置。  
解决方案  
=================
sourceCompatibility = '1.7'
-----------------

假设我项目里包含了这段代码的模块名叫module_A，则在module_A对应的build.gradle里面添加
```
targetCompatibility = '1.7'
sourceCompatibility = '1.7'```
或者
```
compileJava {
    sourceCompatibility '1.7'
    targetCompatibility '1.7'
}
```
一个不适合的解决方案
-----------------
有人提出：
http://www.cnblogs.com/helloshrek/p/6018547.html

这个方案虽然能够消除代码当中的@since error，但是并不能解决我项目中在Build APK途中执行Gradle tasks [:app:assembleDebug]时的的问题

jackOption
---------------

在安卓中使用一些java 8特性的办法就是在你的 app模块里加入
```
android{
defaultConfig{...
  jackOptions{enabled true}
}}
```

jackOption方法的缺点
------------
可看看这个
![](http://i4.buimg.com/4851/e76bd5c07b0da1fd.png)
这就叫做Instant Run与java 8不能兼得啊

开了jackOptions之后，Instant Run就被关闭了，意味着，每次对代码的小改动，整个apk都需要重头重新打包，很可能每次run on device的耗时就是几分钟以上了

此外，这个属性在android库模块里是默认关闭且不能设置为true的，否则会在gradle sync的时候出现错误：  
```Error:Library projects cannot enable Jack. Jack is enabled in default config.```


最后要特别提醒的是 在windows平台上，android默认的内嵌jre可不是Oracle jdk8的，而是自己魔改的open jdk8
当前项目使用的路径可以在Project Structure -> SDK Location标签页里面看到
