---
title: 排雷：gitignore_global在windows环境下的路径写法
date: 2017-05-24 15:33:28
tags: 
- git
- gitignore
- windows
- path
---

# 前情
由于sourcetree最近几个版本更新给我个人感觉非常山寨，而且本来更新后还导致本身运行良好的host auth key chain不再好使了。因而我之前换上了smart git和Github destop app。

今天pull B神的项目然后用webstorm开，发现那一堆/.idea文件出现在了unstaged list里面，很是难受。  
这两个git gui软件里我分别试了一下右键ignore，发现对于添加新ignore行非常不智能。况且这个本身不是我的项目，不想push自己的local ignore行上去。  
因此就想到去改本机的gitignore_global文件。

然则这两个git gui都不提供在gui里面链接global gitignore文件的办法，于是只能求助命令行了

# 用git-bash 设置global gitignore
创建了.gitignore_global文件之后（名字随意的）

`$ git config --global core.excludesfile C:\Users\david_my_name\.gitignore_global`

接下来没有提示信息出现
于是我回到smart git那边看了下，发现那些/.idea/目录下的文件依然在unstaged里。

我又回到了git-bash运行  
`$ git config --global core.excludesfile`

结果出现了

  `C:Usersdavid_my_name.gitignore_global`

这就很尴尬了
# 结论：即使在windows平台下设置global ignore文件时，路径依然使用'/'而非'\\'

路径改回 C:/Users/david_my_name/.gitignore_global就好了

此外，也可以直接在
.gitconfig文件里面直接更改对应配置  
.gitconfig文件常会出现在C:/Users/david_my_name/里
```
[filter "lfs"]
    clean = git-lfs clean -- %f
    smudge = git-lfs smudge -- %f
    process = git-lfs filter-process
    required = true
[user]
    name = davidLiu
[user]
    email = david-khala@hotmail.com
[core]
    excludesfile = C:/Users/david_my_name/.gitignore_global```




