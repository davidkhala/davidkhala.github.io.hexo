---
title: hyperledger/fabric-peer:不存在tag:latest
date: 2017-09-04 18:54:39
tags:
    - 区块链
    - hyperledger
    - fabric
---

latest标签，是docker image的默认标签

官方文档：
>To download a particular image, or set of images (i.e., a repository), use docker pull. If no tag is provided, Docker Engine uses the :latest tag as a default.   
https://docs.docker.com/engine/reference/commandline/pull/#concurrent-downloads

按照当前的分支标签策略，在docker hub的fabric的镜像标签列表中，确实不存在默认标签
所以如果直接用命令`$ docker pull image hyperledger/fabric-peer`，会出现默认标签不存在的问题。  
但是为什么有的朋友没遇到这个问题呢，这就要怪鸡贼的fabric安装脚本了

```
# 这里以1.0.2版本的脚本为例
curl -sSL https://goo.gl/Gci9ZX | bash
```

>脚本出处  
https://hyperledger-fabric.readthedocs.io/en/latest/samples.html#download-platform-specific-binaries

我们在浏览器打开https://goo.gl/Gci9ZX  
可以看到这么一段代码
```
docker tag hyperledger/fabric-$IMAGES:$FABRIC_TAG hyperledger/fabric-$IMAGES
```
这代码的作用就是将本来的以`$FABRIC_TAG`为标签的image，重命名为没有标签的版本
而没有标签，按照docker的规定，就是以latest为标签

所以，脚本在docker pull image的过程当中，其实是有标签的，这个标签是`$FABRIC_TAG`，之后才将这个标签抹去

我个人是非常不支持这个做法的，虽然有在之后的compose文件里面不需要再指定标签的好处，可是一旦出了docker image版本问题，会更难调试。  

而这个情况往往出现在在同一台机器上试运行不同的fabric样例时，由于样例之间的更新进度参差不齐，开发人员一不留神，就会在一个项目里面调用了另外一个版本的fabric image导致奇怪的出错
而使这种情况更糟糕的是

__你可能需要半个多小时才下载下来的image，为了切换到别的项目，不得不被删除，然后再用到的时候，又要等半个多小时__

### 更好的做法是
 - __不要直接运行脚本，将脚本当中下载image的部分分离开来单独操作__
 - __在每一个需要用到fabric docker image的地方，都显式注明所需要的版本标签__



