---
title: ORDERER_GENERAL_GENESISMETHOD的两种模式
date: 2017-09-08 11:05:44
tags:
---

之前有段时间在研究在channel中动态增加组织的方法，突发奇想想试下如果不在orderer中加genesisblock的环境参数会如何
<!--more-->
于是就顺带研究了下标题中的问题

引用官方文档：https://github.com/hyperledger/fabric/blob/master/sampleconfig/orderer.yaml
>\# Genesis method: The method by which the genesis block for the orderer system channel is specified. Available options are "provisional", "file":
>\#  - provisional: Utilizes a genesis profile, specified by GenesisProfile,to dynamically generate a new genesis block.
>\#  - file: Uses the file provided by GenesisFile as the genesis block.
>GenesisMethod: provisional

可知genesismethod有两种模式：provisional 和 file模式
## provisional模式
这种模式利用系统预先定义的的genesisblock和预定义的block profile集合进行orderer初始化
```
// Select the bootstrapping mechanism
	switch conf.General.GenesisMethod {
	case "provisional":
		genesisBlock = provisional.New(genesisconfig.Load(conf.General.GenesisProfile)).GenesisBlockForChannel(conf.General.SystemChannel)
	case "file":
		genesisBlock = file.New(conf.General.GenesisFile).GenesisBlock()
	default:
		logger.Panic("Unknown genesis method:", conf.General.GenesisMethod)
	}
```
源码出处：    

https://github.com/hyperledger/fabric/blob/b81013d18b9c207b7fd3632c71b7b41b55ba02ee/orderer/common/server/main.go#L160

provisional模式需要配合另一个环境参数`ORDERER_GENERAL_GENESISPROFILE`使用  
`ORDERER_GENERAL_GENESISPROFILE`的取值只能是预定义的block profile集合当中选一个  
如下是所有的集合元素
- SampleInsecureSolo
- SampleNoConsortium
	- **警告**  这个模式下orderer容器会无法启动，出现错误：```panic: No system chain found. If bootstrapping, does your system channel contain a consortiums group definition``` 
- SampleInsecureKafka
- SampleDevModeSolo
- SampleSingleMSPSolo

各个类型解释https://github.com/hyperledger/fabric/blob/master/sampleconfig/configtx.yaml

综上: 对于初学者，如果没有特殊的配置要求，可以用内置的类型先创建起来，而又由于内置类型无法更改，正如provisional的字面意思“临时的”，所以只能是权宜之策，真正开发的时候还是需要file模式
## file模式（推荐）

`ORDERER_GENERAL_GENESISMETHOD=file`之后  
需要指定`ORDERER_GENERAL_GENESISFILE`作为orderer初始化时的block文件路径
注意这个路径是orderer容器内的路径不是宿主机的路径  
若不指定，`ORDERER_GENERAL_GENESISFILE`的系统默认值是
`/etc/hyperledger/fabric/genesisblock`  
而本地的block文件可以通过docker volumes挂载的办法同步到ORDERER_GENERAL_GENESISFILE所指定的位置，具体做法见仁见智
_因此如果不指定ORDERER_GENERAL_GENESISFILE又没有将block文件放到默认路径，orderer将由于找不到文件而无法启动_






