---
title: configtx.yaml中两个参数AbsoluteMaxBytes， PreferredMaxBytes的分别
date: 2017-09-14 13:04:30
tags: 
    - 区块链
    - hyperledger
    - fabric
---
今天一位朋友转发了这个文章  
HyperLedger Fabric - HyperLedger Fabric 1.0的Transaction处理流程（分享自知乎网）https://zhuanlan.zhihu.com/p/27645101?utm_source=qq&utm_medium=social  
其中关于orderer block打包的部分引起了我的兴趣
>Orderer节点只是负责排序和打包工作，处理的结果是一个Batch的Transactions，也就是一个Block，这个Block的产生有两种情况，一种情况是Transaction很多，Block的大小达到了设定的大小，而另一种情况是Transaction很少，没有达到设定的大小，那么Orderer就会等，等到大小足够大或者超时时间。这些设置是在configtx.yaml中设定的。

我去查阅了configtx可选的配置大全,但是依然有点疑惑  
https://github.com/hyperledger/fabric/blob/b29c935487867d78935e26c6a3014fd73bd781e1/sampleconfig/configtx.yaml#L185
在这185行和191行中，两个参数文档看着有点相似，那他们到底有什么不同呢  
答案在此https://jira.hyperledger.org/browse/FAB-1243

**无论message池（未打包的交易）有多少数量，尽量让每个block 大小不超过PreferredMaxBytes。**  
- Cut blocks to no more than PreferredMaxBytes, regardless of number of messages.   


**如果单条message大小就已经超过了PreferredMaxBytes，那么他在不超过AbsoluteMaxBytes的前提下会被单独打包成一个block**  
- Messages that exceed PreferredMaxBytes will result in a batch of 1 (just that message), as long as message does not exceed AbsoluteMaxBytes.


伪代码
```
onTimeOut=()=>{
    batch(txPool)
    //next loop
    setTimeOut(onTimeOut,BatchTimeOut)
}
setTimeOut(onTimeOut,BatchTimeOut)
...
onNewTx=(newTx)=>{

    if(newTx.size>PreferredMaxBytes)
        {
        if(newTx.size<AbsoluteMaxBytes){
            batch(txPool) // cut block
            batch([newTx])
        }else{
            throw new Error(reject("bad request"))
        }
    
    }else{
        txPool=txPool.concat(newTx)
        if(txPool.size<MaxMessageCount)
        {
        
        }
        else{
            batch(txPool)
        }
    }
}

```

