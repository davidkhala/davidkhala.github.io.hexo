---
title: Android中通过typeface设置字体
date: 2017-10-29 22:03:06
tags:
- Android
---
今天整理过去的笔记时，发现之前我记录了一个关于android typeface字体设置的辟谣笔记
这里分享出来

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
