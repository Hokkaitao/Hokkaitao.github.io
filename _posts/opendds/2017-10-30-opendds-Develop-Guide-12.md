---
layout: post
published: true
title: "OpenDDS 开发手册- 12章 Recorder and Replayer"
description: opendds
---
## 综述

OpenDDS 的Recorder特性允许应用程序在不需要了解数据类型的前提下记录任意主题下样本的发布。类似的，Replayer特性允许这些记录的样本以相同或是不同的主题重新进行发布。和其他的Data Readers和Writers所不同的是其可以在任何类型下正常工作，甚至在编译阶段并不知道数据类型。样本的每一行包含不透明的字节序列。

本章节的目的是描述recording/replaying工作的OpendDDS所提供的公共API。

## API Structure

两个新的用户可见的类被定义在OpenDDS:DCPS命名空间下，其包含Listener接口。Listerners应用程序可选进行实现。Recorder类扮演的角色类似于DataReader，Replayer类扮演的角色类似于DataWriter。

Recorder和Replayer使用OpenDDS底层的发现和传输库，就好像DataReader和DataWriter一样。正常的在域中的OpenDDS应用程序将会感知到Recorder同远程DataReader一样，而Replayer就好像DataWriter一样。

## 模型用法

应用程序可以按需创建任意数量的Replayer和Recorder。这是基于内建主题在域中动态发现哪些主题是活跃状态的。创建Recorder或是Replayer需要应用程序提供主题名和类型名（如同DomainParticipant::create_topic()）以及相关的QoS数据结构。Recorder需要SubscriberQos，然而Replayer需要PublisherQos和DataWriterQos。这些值在reader/writer匹配时使用。查看QoS处理相关章节来获得Recorder和Replayer如何使用Qos。下面代码创建一个recorder：

```
OpenDDS::DCPS::Recorder_var recorder = 
	Service_participant->create_recorder(domain_participant, 
						topic.in(), 
						sub_qos, 
						dr_qos, 
						recorder_listener);
```

通过RecorderListener使用“one callback per sample”模型来使得数据样本可用。样本作为OpenDDS::DCPS::RawDataSample对象。该对象包括数据样本的时间戳以及排序的样本值。下面是一个用户定义的RecorderListener类定义：

```
class MessagerRecorderListener: public OpenDDS::DCPS::RecorderListener
{
public:
	MessagerRecorderListener();
	virtual void on_sample_data_received(OpenDDS::DCPS::Recorder*, 
						const OpenDDS::DCPS::RawDataSample &sample);
	virtual void on_recorder_matched(OpenDDS::DCPS::Recorder*, 
						const DDS::SubscriptionMatchedStatus &status);
}
```

应用程序可以存储到合适的地方（内存、文件系统、数据库等等）。后续，应用程序可以将相同的样本提供给配置了相同主题的Replayer对象。应用程序有责任保证主题类型匹配。下面是replays将样本发送给已连接的所有readers的例子：

```
replayer->write(sample);
```

由于存储的数据依赖于数据结构的定义，所以参与的OpenDDS所使用的版本号和IDL版本应该一直。

## QoS 处理

由于缺失数据样本详细的信息，使得许多在Replayer端的正常的DDS QoS属性使用变得复杂。属性可以分为以下几类：

```
Supported
	- Liveliness
	- Time-Based Filter
	- Lifespan
	- Durability(transient local level, see details below)
	- Presentation(topic level only)
	- Transport Priority(pass-thru to transport)
Unsupported
	- Deadline(still used for reader/writer match)
	- History
	- Resource Limits
	- Durability Service
	- Ownership and Owership Strength(still used for reader/writer match)
Affects reader/writer matching and Built-in Topics otherwise ignored
	- Partition
	- Reliability(still used by transport negotiation)
	- Destination Order
	- Latency Budget
	- User/Group Data
```

## 持久性细节

在Recorder端，短暂的本地持久化工作与任何正常的DataReader类似。持久化数据从匹配的DataWriters获得。在Replayer端有些不同。与正常的DDS DataWriter相反，Replayer不会捕获/存储任何数据样本（其只是传输）。由于实力并不知道，因此根据通常历史和资源限制规则存储数据样本是不可能的。相反，短暂本地持久化可以支持“pull”，这是当一个新的远端DataReader被发现时，中间件调用ReplayerListener的方法来实现的。应用程序可以Replayer上调用任何数据样本来发送给新连接上的DataReader。决定哪些样本是留给应用端确定的。

## 参考
- [OpenDDS 开发手册-12章](http://download.objectcomputing.com/OpenDDS/OpenDDS-latest.pdf)
