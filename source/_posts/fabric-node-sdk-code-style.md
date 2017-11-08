---
title: fabric-node-sdk鲜为人知的代码规范要求
date: 2017-11-07 09:40:03
tags: 
 - 区块链
 - hyperledger
 - fabric
 - fabric-node-sdk

---

在跟Jim Zhang用patch交流的过程中，他提到了一些代码规范的点，在此总结一下
<!--more-->
 - 语句最后要以‘;’结尾
 - 不要使用fs.readFileSync(??这点有待商榷)
 - 不要遗留空代码行