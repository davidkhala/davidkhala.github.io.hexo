---
title: fabric维度的思考
date: 2017-09-04 17:56:22
tags: 
    - 区块链
    - hyperledger
    - fabric
---

本文不是fabric推广文章，虽然会提及fabric的优点，但是更多想提醒各位fabric的局限性
本文不是技术入门文章，不会告诉你怎么学习fabric，也不会告诉你怎么使用fabric
本文也不是理念介绍文章，虽然会按照fabric的架构来讲，但是不会详解各个模块的功能和划分依据

本文是关于以下几点
# 拓展性：
1. 哪些位置是fabric鼓励各位开发可以去拓展，构建自己的应用的
2. 哪些位置是fabric没有明说，但是在当前稳定版本下实践证明可以进行拓展的
3. 哪些位置是fabric不鼓励的操作，但是在某些版本下依然可以通过入侵式的方法做到的

# 维度： 拓展性1
![image](https://hyperledger-fabric.readthedocs.io/zh-cn/latest/_images/network.diagram.1.png)
## 通道

通道是一个完全隔离的空间，它可以看成是一个独立的区块链


## MSP
在通道配置里
/{channel}/orderer/mspid[]
/{channel}/application/mspid[]

## 节点

账本物理上托管在peer节点上，逻辑上托管在通道上

节点加入通道后，存留着通道的配置块

节点的启动需要msp_config, 通常为服务器的私钥以及签发的证书，
- 加入通道时，被通道上的msp根证书验证
- 客户端安装链码时，本地式地验证客户端

## 状态

私有数据集PDC（包括implict collection）：/{channel}/{chaincode}/{collection}/
全局状态world state：/{channel}/{chaincode}/

## 链码
链码安装在节点上
/{chaincode}
定义在通道里
/{channel}/{chaincode}
实例化在容器中或者CaaS服务中

# 开发效率瓶颈：
哪些位置是开发调试时特别需要小心的，由于区块链的不可逆性，一旦写入就没法撤销而导致的测试周期缓慢的

- 加入通道后无法退出
- 安装链码后无法卸载
- 链码实例化后，更新链码并不会自动清理旧实例化容器
- 全局状态存储引擎选择后，无法轻易切换











------ 未完待续-----


