---
title: fabric-node-sdk Jobbuilder的一些格式限制
date: 2017-10-26 09:40:58
tags:
 - 区块链
 - hyperledger
 - fabric
 - git
---

最近我提交了一个patch，遭到了Hyperledger Jobbuilder的反复'拒绝'。在此想分享一下拒绝原因

JS doc
-------------------
 * 单行不能超过150字符
 * 我最终成功的jsdoc格式： `@property {boolean} skipPersistence - whether to save this new user object into persistence.`注意，‘whether’前面的hyphen不能省略！