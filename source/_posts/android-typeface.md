---
title: Android中通过typeface设置字体
date: 2017-10-29 22:03:06
tags:
- Android
---
今天整理过去的笔记时，发现之前我记录了一个关于android typeface字体设置的辟谣笔记
这里分享出来

<!--more-->

http://www.cnblogs.com/bravestarrhu/archive/2012/07/17/2595598.html
链接的文章里面有这么一段话：

>//将字体文件保存在assets/fonts/目录下，创建Typeface对象
>Typeface typeFace =Typeface.createFromAsset(getAssets(),"fonts/HandmadeTypewriter.ttf");
>//使用字体
>textView.setTypeface(typeFace);

经过测试发现，fonts目录并不是关键字，直接放在asset根目录里面也是一样可以的
```
Typefacecustom_font=Typeface.createFromAsset(getAssets(),"enter_the_grid.ttf");
```
这样就足够了

-------------

## PS 喜报 Oreo开始终于支持将字体文件作为资源放置在res目录下
>Android 8.0 (API level 26) introduces a new feature, Fonts in XML, which lets you use fonts as resources. 

来自 <https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html> 

-------------

## 彩蛋：当EditText的InputType属于password大类时，无法给EditText设置字体

password的变体包括：
- textPassword
- numberPassword
- textVisiblePassword
- textWebPassword

以上4种变体，**除了textVisiblePassword之外**，都是无法通过android:fontFamily或者其他方式设置字体的

> 为什么要给被覆盖了的密码输入框设置字体呢？

因为，像``android.support.design.widget.TextInputLayout``这类的输入框装饰器里，浮动提示或者placeholder型提示是可见的，意味着还是有空间让特定字体去发挥的。



