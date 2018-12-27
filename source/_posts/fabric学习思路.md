title: fabric学习思路
author: yanwei
tags: []
categories: []
date: 2018-11-18 20:45:00
---
![upload successful](https://user-images.githubusercontent.com/16861688/50393715-1545e580-0793-11e9-920b-1790df0c60db.png)
必备知识点通常包括以下几部分。

Docker
Docker 是一个开源的应用容器引擎，基于 Go 语言并遵从Apache2.0协议开源。 Docker 可以让开发者将应用和依赖包打包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。 容器完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 App），更重要的是容器性能开销极低。

在学习HyperLedger Fabric的过程中，开发人员需要掌握Docker的基本操作命令，以便可以更快上手，这其中包括打包、拆包、tag、容器管理、镜像管理等。同时，也需要学习Docker Compose和YAML的编排方案，了解如何挂载文件路径、设置端口绑定、确定Network以及掌握环境变量的用法等。

Go
Go是一种并发的、带垃圾回收的、快速编译的语言。它可以在一台计算机上用几秒钟的时间编译一个大型的Go程序，结合了解释型语言的游刃有余、动态类型语言的开发效率以及静态类型的安全性。

HyperLedger Fabric是一个基于Go开发的开源项目，学习Go不仅仅是为了更深入地阅读源码需求，也因为智能合约编写方案需要Go来实现。对于Go的学习，至少能够在参考API文档的情况下编写出一套符合需求的智能合约。然而，对于有一定面向对象基础的开发人员来说，这并不难。

gRPC
gRPC 是一个高性能、通用的开源RPC框架，由 Google公司 主要面向移动应用开发并基于HTTP/2 协议标准而设计的，基于 ProtoBuf(Protocol Buffers) 序列化协议开发，且支持众多开发语言。

在学习HyperLedger Fabric的过程中，对gRPC的感知并不深，但SDK的相关项目都需要用到它。建议还是需要了解一下诸如此类的传输协议，与之类似的还有Thrift、Dubbo等。

CA
CA是PKI系统中通信双方信任的实体，被称为可信第三方（Trusted Third Party， TTP）。作为可信第三方的行为具有非否认性。

在HyperLedger Fabric体系中，CA被广泛运用于每一个具体的对象，如orderer、peer以及最小单位的user等。后端开发的工程师应该比较了解，在HyperLedger Fabric中CA被用于各种对象的登记、登出、注册、撤销等操作。

P2P
Peer-to-Peer，对等网络，即对等计算机网络，是一种在对等者（Peer）之间分配任务和工作负载的分布式应用架构。

关于P2P的学习并非必须，但也算是入门区块链底层的必修课，对期望今后有机会进行底层开发的工程师来说，目前多少需要了解一些。

Kafka
Kafka是一个分布式消息系统，由LinkedIn使用Scala编写，用作LinkedIn的活动流（Activity Stream）和运营数据处理管道（Pipeline）的基础。具有高水平扩展和高吞吐量。

在Fabric网络中，数据是由Peer节点提交到Orderer排序服务，而Orderer相对于Kafka来说相当于上游模块，且Orderer还兼具提供对数据进行排序及生成符合配置规范及要求的区块。当使用上游模块的数据计算、统计、分析时，可以使用Kafka这样的分布式消息系统来协助业务流程。

Kafka是一种共识模式，也就是平等信任（同步复制），所有的HyperLedger Fabric网络加盟方都是可信方，因为消息总是均匀地分布在各处。但具体生产使用的时候会依赖于背书来做到确权，相对而言，Kafka应该是一种启动Fabric网络的模式或类型。

Zookeeper
Zookeeper是一种在分布式系统中被广泛用于分布式状态管理、分布式协调管理、分布式配置管理和分布式锁服务的集群。

Kafka增加和减少服务器都会在Zookeeper节点上触发相应的事件，Kafka系统会捕获这些事件，进行新一轮的负载均衡，客户端也会捕获这些事件进行新一轮的处理。

SDK（Java/Node.js）
HyperLedger Fabric最终需要落地于实际应用，相对于HyperLedger Fabric的应用层则会直接提供接口给其他各客户端并对区块链网络进行操作。目前，基于HyperLedger Fabric的SDK方案有很多，但相对较成熟的主要还是Java和Node.js的。所以，这两门语言应当是学习HyperLedger Fabric平台工程师的主力语言，建议熟练使用两者其中之一。

如果这两门语言都不是目前学习Hyperledger Fabric工程师的主力语言，可以考虑用其他被gRPC所支持的编程语言自行实现，或者使用Fabric-SDK-Container提供的解决方案。