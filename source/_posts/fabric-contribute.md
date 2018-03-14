---
title: 如何给Hyperledger-fabric贡献代码
date: 2017-08-30 10:29:39
tags: 
    - 区块链
    - hyperledger
    - fabric
    - gerrit
---

本文整理了刘文亮的指引，仅作为对https://yeasy.gitbooks.io/blockchain_guide/hyperledger/contribute.html
的补充说明，并一并感谢

<!--more-->
## 安装环境
 - git
 - gerrit所需的组件：git-review，安装方法见 https://www.mediawiki.org/wiki/Gerrit/git-review

## 取得权限
注册 Linux foundation ID，就是linux基金会的账号，简称为LFID 

*gerrit基本不支持匿名操作*

## code of contributor贡献者守则

代码是为了修复jira上提出的issue的，如果是新功能，也要创造一个新issue去‘修复’  
记下我们要修复的问题的序号，比如FAB-XXXX，我们后面会用到

## 第一次提交流程
1. 进入https://gerrit.hyperledger.org/，并登陆你的LFID  
    *登陆入口在右上方，Search按钮下面有个‘Sign in’*  
2. 查看项目列表，找到对应项目，这里以 lf-sandbox 项目为例子  
    *lf-sandbox项目不是 fabric项目，而是官方推荐拿来学习 gerrit用的沙盒项目*

3. 点选左上方的`Clone with commit-msg hook`按钮  
    *错误示例：如果没有登陆你的 LFID，你将看不到 `Clone with commit-msg hook`按钮，只能看到 anonymous http 命令`git clone https://gerrit.hyperledger.org/r/lf-sandbox`*
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
6. **更新** 重命名远端 `$ git remote rename origin gerrit`
    如果没重命名，那么后面在执行 `$ git review --setup`时，由于的确没有.gitreview文件，因此一定会出现_
  > No '.gitreview' file found in this repository. We don't know where your gerrit is. Please manually create a remote named gerrit and try again.  
6. **更新** 初始化gerrit：`$ git review --setup`  
 \--setup，代表只初始化仓库但不提交 _Just run the repo setup commands but don't submit anything_  
 - 初始化时的工作包括（参见https://www.mediawiki.org/wiki/Gerrit/git-review#Setting_up_git-review）
  > 测试远端仓库连通性，若失败，会要求输入一个gerrit用户名，输入LFID即可
  >创建一个名为“gerrit”的远端并引向它 
  >安装commit-msg hook
 - 若跳过此部，在后面第一次运行git review时`git review --setup`仍然会被自动运行一次，但是**在clone之后紧接着运行一次**是推荐的最佳实践
7. 创建一个branch，名字格式为 FAB-XXXX （issue序号）  
      ```
      $ git checkout -b FAB-XXXX
      ```
8. 正式干活了，修复issue
9. 提交 ：`$ git commit -a -s`
   *-s 选项是用于在commit message中自动生成sign off签名的*
10. git会进入你设置的文本编辑器里编辑commit message
    message推荐以这种格式编写

      > [FAB-XXXX] <标题,官方文档说本行不能超过72个字符，实测不能超过55个字符>  
      > <空行>
      > This fixes #FAB-XXXX  
      > <内容：每行不超过80字符，其中应该包括
      >     - 你的提交做了什么？
      >     - 为何选用这种方式去进行改动
      >     - 为何这种改动能够成功，比如提交你成功的代码测试结果
      >     
      > >

11. 推出送申： `$ git review`  
    *gerrit不用 push作为推出*

## 代码评审和修改
提交成功之后，可以去gerrit.hyperledger.org/r/上查看自己最新提交的patchSet信息
0. 检查你的变更请求Change Request(CR)是否通过了Hyperledger JobBuilder检测
为了确保代码的稳定性，官方基于Jenkins设立了一套持续集成（CI）流程。这套流程会在每次patchSet被推出之后，尝试在各种平台上运行测试。
贡献者有责任去保证自己的代码能供通过这些测试
测试通过后JobBuilder会自动给你的变更投票 +1，否则则会投 -1
    **注意，有的时候CI流程会因为莫名的原因而出错，所以你需要去看log来确保不是自己的锅。**
    如果确实跟你无关，你可以通过在你的CR页面上回复'reverify'来重启CI流程。
    
    关于CI流程的更细节信息详见
    - https://github.com/hyperledger/ci-management/blob/master/docs/fabric_ci_process.md
    
    **号外**: @rameshthoomu 提供了专属node-sdk的更原子化的reverify关键字，回复其中一个可以单独重启某个CI子过程。
    这样只需要重启错误的那一两个子过程，而不是重启整个CI，来加速验证迭代  
    如：在x86_64上的用``reverify-node8x``,在s390x上的用``reverify-node8z``
    此外，现在node6版本的平台测试也已经不再是默认包含的了
        
    

1. 添加审阅者Add Reviewer
一般都是添加一些活跃并具有影响力的大拿，比如BaohuaYang，Gari Signh，node-sdk的维护者Jim Zhang，Bret Harrison，核心机制维护Yacov Manevich  
当然最有话语权的还是你所提交的目标仓库的维护者，务必是要邀请他加入审阅的，只有维护者才有权限对你的patch进行merge
    - 如何找到维护者：以node-sdk为例 维护者列表在：
    https://github.com/hyperledger/fabric-sdk-node/blob/master/MAINTAINERS.md
    - 如何快速联系维护者：在rocketChat的PR专属频道发布关于你变更的消息 #fabric-pr-review

    **审阅者列表在change页的中间位置**

2. 更新issue状态
Gari Singh指出
 > once you submit a CR for review, please make sure you update the status of the JIRA to "In Review" and add the CR link in a comment    
“在你的CR提交之后，请将jira上issue的状态变更为‘In Review’，并且添加一条包含该CR链接的评论”

3. 修改提交
如果评审没有通过，即有Reviewer给你的patchSet评论并扣分了，则需要做进一步的修正
 > 修正过程跟第一次提交类似，在当前的branch：FAB-XXXX里面修改好之后，使用如下命令修改提交 
    ```
    $ git commit -a --amend
    ```
 > 接着再进行git review就可以了

4. 合并
如果仓库维护者认可了你的patch，他会给你的最终patchSet加分并回复说你的change将会被合并到主分支上

## 与别人协作

感谢Dave Enyeart的提醒
 > Simply reach out to the initial contributor either via rocket.chat or gerrit review or jira to coordinate, and then you can Checkout the latest patch under gerrit Download link, make your updates, `git commit -s --amend` to update and sign the commit, then push as normal.

1. 出于礼貌，先跟原作者通过rocketChat，gerrit review或者jira issue的方式联系沟通，表达你愿意在他的CR做进一步修改的意愿。
2. 在patch页面的右上角，账户名字的左下方，你能看到一个名为 Download 的下拉菜单，复制运行它所提供的Checkout命令，如:  
 ```
 $ git fetch https://davidkhala@gerrit.hyperledger.org/r/a/fabric-sdk-node refs/changes/09/11609/2 && git checkout FETCH_HEAD
 ```
 运行后，branch就会下载到你本机上
3. 下载后，当前的仓库会处于一个 detached head上，为了方便管理，应该在这个head上创建一个branch（同上）
 ```
 $ git checkout -b FAB-XXXX
 ```
4. 在branch FAB-XXXX上进行修改
5. 提交时使用修改提交模式
 ```
    $ git commit -a --amend -s
 ```
6. 推出方式同上 $ git review












    


