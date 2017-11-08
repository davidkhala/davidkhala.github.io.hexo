---
title: 对比node-sdk，fabric-java-sdk开发e2e测试的一些注意点
date: 2017-11-08 14:48:07
tags:
---

与node-sdk相比有利有弊，由于Java本身机制原因，代码简洁，对象访问权限也控制的更好，虽然没有文档，但是通过阅读   
``../src/test/java/org/hyperledger/fabric/sdkintegration/End2endAndBackAgainIT.java``
和同目录下的``End2endIT``也足够进行开发工作了

测试代码会在这里更新：https://github.com/davidkhala/delphi-fabric/tree/master/JavaSDKTest
<!--more-->

_____________________
### name属性：
相对于node-sdk里面的对象，很多java-sdk的对象，包括Peer，EventHub对象，都多了一个会进行检查的name属性，根据我观察，除了作为一个错误日志里的标签之外，没有别的用途

### Client对象
```
    //获取Client对象的方法
    HFClient client = HFClient.createNewInstance();
    client.setCryptoSuite(CryptoSuite.Factory.getCryptoSuite());//设CryptoSuite为默认的格式，对应node-sdk里面的BaseClient.newCryptoSuite()
```
 * 由于Channel和Client的紧密关系，经常需要在用Channe时获取它蕴含的Client对象，我自己准备了一个静态方法通过反射机制来获取它
    ```
    public static HFClient getClient(Channel channel) throws NoSuchFieldException, IllegalAccessException {
            Field f = Channel.class.getDeclaredField("client"); //NoSuchFieldException
            f.setAccessible(true);
            return (HFClient) f.get(channel);
    }
    ```
### User对象  
 - User对象是一个接口，其中需要实现的方法是各类getter，其中比较重要的除了name之外，还有``String MspId;``和``Enrollment enrollment;``  
 - ``Enrollment``对象依然是个接口，用来保存PrivateKey和证书``String cert``的，在使用User之前请务必将enrollment设置正确。
 - 对于由``cryptogen``生成的目录结构来说，PrivateKey可以对应``msp/keystore``下面的*_sk文件，证书对应``msp/signcerts``下面的证书文件
 - 官方读取PrivateKey的方案:
    ```
        //通过sdk内置的Bouncy Castle库来导入的
        static PrivateKey getPrivateKeyFromBytes(byte[] data) throws IOException, NoSuchProviderException, NoSuchAlgorithmException, InvalidKeySpecException {
            final Reader pemReader = new StringReader(new String(data));
            final PrivateKeyInfo pemPair;
            PEMParser pemParser = new PEMParser(pemReader);
            pemPair = (PrivateKeyInfo) pemParser.readObject();

            return new JcaPEMKeyConverter().setProvider(BouncyCastleProvider.PROVIDER_NAME).getPrivateKey(pemPair);
        }
    ```
 - 同样的，User对象准备好之后将其设到Client里``client.setUserContext(user)``

## Channel对象
由于channel的工厂构造方法和构造器都是包私有的，我们一般通过``client.newChannel(...)``来获取channel对象。该方法有两个重载
 * ``newChannel(String name)`` 不会造成实际的影响，单纯只是新建一个对象
    - 相当于Client.js里的``newChannel(name)``，通常用于当channel已经在fabric网络上存在的时候
 * ``newChannel(String name, Orderer orderer, ChannelConfiguration channelConfiguration, byte[]... channelConfigurationSignatures)``
    - 相当于Client.js里面的``createChannel(request)``，向Orderer发出请求去创建channel
    - ``channelConfiguration``通过``new ChannelConfiguration(channelFile)``生成
    - ``channelConfigurationSignatures``可以通过``client.getChannelConfigurationSignature(channelConfiguration, client.getUserContext())``生成
 * 无论通过哪种方式生成，都可以在之后执行``channel.initialize()``
    * 由于在java-sdk中，EventHub的``connect()``方法是包私有的，而调用它的唯一公有方法是channel.initialize，因此eventHub不能像node-sdk那样独立操作，而必须先通过``channel.addEventHub(eventHub)``添加到channel，然后再次运行channel.initialize，才可以让这些eventHub启动

### Chaincode
无论是install还是instantiate，在对应的ProposalRequest里都会需要``setChaincodeID(chaincodeID)``，但是要注意: chaincodeID是一个ChaincodeID类的对象而不是String，而它除了chaincodeName之外还包括version，path这两个信息，这与node-sdk中的chaincodeID == chaincodeName语义不同,
    ```
   ChaincodeID chaincodeID = ChaincodeID.newBuilder().setName(ccName).setVersion(ccVersion).setPath(ccPath).build();
    ```
   * transientMap必须非null，否则会报错  
    ```instantiateProposalRequest.setTransientMap(new HashMap<>())```
   * 对于instantiate和invoke来说，第一段提交Proposal之后需要通过``channel.sendTransaction(successResponses)``进行第二段提交，
       * 其中successResponses为第一段提交返回结果当中，getStatus() == Success的部分
       * 第二段提交会返回一个CompletableFuture<TransactionEvent>结果``future``，如果之前在channel当中正确设置了eventHub，执行成功的交易可以直接通过``BlockEvent.TransactionEvent successEvent = future.get()``取出来。这点相比node-sdk提供了更多的方便，因为node-sdk好像只提供了eventHub注册监听器的方式获得时间
           * 如果eventHub没有设置好，那么程序会一直等待get()方法返回
           * java-sdk也支持注册监听器的方法来获取更多的事件内容，详情见``channel.registerBlockListener(blockEvent->{...})``方法

