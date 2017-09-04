---
title: 如何给Hyperledger-fabric贡献代码
date: 2017-08-30 10:29:39
tags: 
    - 区块链
    - hyperledger
    - fabric
---

本文整理了刘文亮的指引，仅作为对https://yeasy.gitbooks.io/blockchain_guide/hyperledger/contribute.html
的补充说明，并一并感谢

## 安装环境
 - git
 - gerrit所需的组件：git-review，安装方法见 https://www.mediawiki.org/wiki/Gerrit/git-review

## 取得权限
注册 Linux foundation ID，就是linux基金会的账号，简称为LFID 

*gerrit基本不支持匿名操作*

## code of contributor贡献者守则

代码是为了修复jira上提出的issue的，如果是新功能，也要创造一个新issue去‘修复’  
记下我们要修复的问题的序号，比如FAB-XXXX，我们后面会用到

## 获取代码
1. 进入https://gerrit.hyperledger.org/，并登陆你的LFID  
    *登陆入口在右上方，Search按钮下面有个‘Sign in’*  
2. 查看项目列表，找到对应项目，这里以 lf-sandbox 项目为例子  
    *lf-sandbox项目不是 fabric项目，而是官方推荐拿来学习 gerrit用的沙盒项目*

3. 点选左上方的`Clone with commit-msg hook`按钮  
    *错误示例：如果没有登陆你的LFID，你将看不到 `Clone with commit-msg hook`按钮，只能看到 anonymous http 命令`git clone https://gerrit.hyperledger.org/r/lf-sandbox`*
    *匿名clone下来的项目在后面提交时会出现commit hook缺失的错误*
4. 在按钮同一栏的右边，选择验证方式 ssh|http，并复制其下方的clone命令
    - ssh验证：需要先生成本机的ssh pubkey文件，并在https://gerrit.hyperledger.org/的个人设置中将pubkey的内容加入列表中，才可以使用  
    好处：操作时不用交互输入密码
    坏处：每台机器的pubkey都需要加入个人设置中，某些情况下会因为防火墙timeout  
    - http验证 **推荐**：  
    clone命令形如：
    ``` 
    git clone https://<LFID>@gerrit.hyperledger.org/r/a/lf-sandbox && (cd lf-sandbox && curl -kLo `git rev-parse --git-dir`/hooks/commit-msg https://<LFID>@gerrit.hyperledger.org/r/tools/hooks/commit-msg; chmod +x `git rev-parse --git-dir`/hooks/commit-msg)
    ```
5. 选择一个父目录，执行刚才复制的clone命令，下载代码
6. 创建一个branch，名字格式为 FAB-XXXX （issue序号）  
      ```
      $ git checkout -b FAB-XXXX
      ```
7. 重命名远端为gerrit: `$ git remote rename origin gerrit`
      *否则在git review时会提示:*
      > No '.gitreview' file found in this repository. We don't know where your gerrit is. Please manually create a remote named gerrit and try again.
8. 正式干活了，修复issue
9. 提交 ：`$git commit -a -s`
   *-s 选项不能漏，因为是帮你在commit message中自动生成sign off签名的*
10. git会进入你设置的文本编辑器里编辑commit message
    message推荐以这种格式编写

      > <标题>  
      > This fix #FAB-XXXX  
      > <内容>

11. `$ git rebase master` 
       *可能是为了生成channge-id*
12. 推出送申： `$ git review`  
    *gerrit不用 push作为推出*

    










    

