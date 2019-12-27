---
title: 消息中间件-ActiveMQ
date: 2017-12-28 22:15:35
categories:
- 后端
tags:
- java
---

## java消息中间件

为什么使用消息中间件？

* 解耦
* 异步
* 横向扩展
* 安全可靠
* 顺序保证(kafka)

### 概述

什么是中间件：

非底层操作系统软件，非业务应用软件，不是直接给最终用户使用的，不能直接给客户带来价值的软件统称为中间件

消息中间件：

关注于数据的发送和接受，利用高效可靠的异步消息传递机制集成分布式系统

应用A->接口->消息中间件接受数据

应用B->接口->消费消息中间件数据

### 	JMS

java消息服务 java message service 

是一个java平台中关于面向消息中间件的API，用于在两个应用程序之间火分布式系统中发送消息，进行异步通信

<!--more-->

### AMQP

advanced message queuing protocol是一个提供统一消息服务的应用层标准协议，基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同开发语言邓条件的限制

### JMS和AMQP对比

|      | JMS规范                                    | AMQP协议                                   |
| ---- | ---------------------------------------- | ---------------------------------------- |
| 定义   | java api                                 | Wire-protocol                            |
| 跨语言  | 否                                        | 是                                        |
| 消息类型 | 提供两种消息模型：p2p， pub/sub                    | 提供了五种消息模型：direct,fanout,topic,headers,system |
| 消息类型 | TextMessage,MapMessage,<br />BytesMessage,StreamMessage,ObjectMessage,Message | byte[]                                   |
| 综合评价 | JMS定义了JAVA API层面的标准，在java体系中，多个client 均可以通过JMS进行交互，不需要应用修改代码，但是其对跨平台的支持较差 | AMQP的主要特征是面向消息，队列、路由（包括点对点和发布/订阅）、可靠性、安全 |

### 常见消息中间件对比

ActiveMQ特性

> 多种语言和协议编写客户端。
>
> 完全支持JMS1.1和J2EE1.4规范（持久化，XA消息，事务）
>
> 虚拟主题，组合目的，镜像队列

RabbitMQ

>支持多种客户端
>
>AMQP的完整实现（vhost，exchange,binding，routing key等）
>
>事务支持/发布确认
>
>消失持久化

Kafka

>是一种高吞吐量的分布式发布订阅消息系统，是一个分布式的，分区的，可靠的分布式日志式存储服务，它通过一种独一无二的设计提供了一个消息系统的功能
>
>通过O(1)的磁盘数据结构提供消息的持久化，这种结构对于即使数以TB的消息存储也能保持长时间的稳定性能
>
>高吞吐量：即使是非常普通的硬件Kafka也可以支持每秒钟百万的消息
>
>Partition，consumer group

{% asset_img 综合评价.jpg image %}

## JMS规范

概念：

* 提供者：实现JMS规范的消息中间件服务器
* 客户端：发送或接收消息的应用程序
* 生产者/发布者：用来创建和发布消息的客户端
* 消费者/订阅者：用来接收并处理消息的客户端
* 消息：应用程序之间穿的的数据内容
* 消息模式：在客户端之间传递消息的方式，JMS中定义了主题和队列两种模式

### JMS消息模式

**队列模型** queue

* 客户端包括生产者和消费者
* 队列中的消息只能被一个消费者消费
* 消费者可以随时消费队列中的消息

{% asset_img 队列模型1.jpg image %}

**主题模型** topicQueue

* 客户端包括发布者和订阅者
* 主题中的消息被所有订阅者消费
* 消费者不能消费订阅之前就发送到主题中的消息

{% asset_img 主题模型.jpg image %}

### JMS规范

**JMS编码接口**

* ConnectionFactory 用于创建连接到消息中间件的连接工厂
* Connection代表了应用程序和消息服务器之间的通信链路
* Destination 指消息发布和接收的地点，包括队列或主题
* Session 表示一个单线程的上下文，用于发送和接收消息
* MessageConsumer 有会话创建，用于接收发送到目标的消息
* MessageProducer由会话创建，用于发送消息到目标
* Message 是在消费者和生产者之间传送的对象，消息头，一组消息属性，一个消息体

{% asset_img 接口关系.jpg image %}

```
队列模式：平均接收消息生产者产生的消息
主题模式：全部接收生产者产生的所有消息
```

### 使用Spring集成JMS连接ActiveMQ

* ConnectionFactory用于管理连接的连接工厂
* JmsTemplate用于发送和接收消息的模板类
* MessageListerner消息监听器

#### ConnectionFactory

* 一个spring为我们提供的连接池
* JmsTemplate每次发消息都会重新创建连接，会话和productor
* spring中提供了SingleConnectionFacotry（使用一个连接操作）和CachingConnectionFactory（继承前者，新增了缓存功能）

#### JmsTemplate

* 是spring提供的，只需要向spring容器内注入这个类就可以使用JmsTemplage方便的操作jms
* JmsTemplate是线程安全的，可以在整个应用范围内使用

#### MessageListener

* 实现一个onMessage方法，该方法只接收一个Message参数

---

## ActiveMQ集群

为什么要对消息中间件集群？

* 实现高可用，以排除单点故障引起的服务终端
* 实现负载均衡，以提升效率为更多客户提供服务

### 基础知识

* 客户端集群：让多个消费者消费同一个队列
* Broker cluster：多个Broker之间同步消息
* Master Slave：实现高可用

客户端配置：

ActiveMq失效转移（failover）

允许当其中一台消息服务器宕机时，客户端在传输层上重新连接到其他消息服务器

语法：`failover:(uri1,...,uriN)?transportOptions`

**transportOptions参数说明**

* randomize 默认为true 表示在URI列表中选择URI连接时是否采用随机策略
* initialReconnectDelay ， 默认为10，单位毫秒，表示第一次尝试重连之间等待的时间
* maxReconnectDelay 默认为30000，单位毫秒，最长重连的时间间隔

### Broker Cluster集群配置

原理：

node A  --消息同步--> node B

node A <-- 消息同步 -- node B

#### NetworkConnector（网络连接器）

主要用于配置ActiveMQ服务器与服务器之间的网络通讯方式，用于服务器透传消息

分为静态连接器和动态连接器

静态：

```
<networkConnectors>
	<networkConnector uri = "static:(tcp://127.0.0.1:61617,tcp://127.0.0.1:61618)"/>
</networkConnectors>
```

动态：

```
<networkConnectors>
	<networkConnector uri="multicast://default"/>
</networkConnectors>

<transportConnectors>
	<transportConnector uri="tcp://localhost:0"
	discoveryUri="multicast://default"/>
</transportConnectors>
```

### Master/Slave集群配置

* Share nothing storage master/slave（已过时，5.8+后移除）
* Shared storage master/slave 共享存储
* Replicated LevelDB Store 基于复制的LevelDB Store

#### 共享存储集群的原理

| --             | 高可用  | 负载均衡 |
| -------------- | ---- | ---- |
| Master/Slave   | 是    | 否    |
| Broker Cluster | 否    | 是    |

#### 三台服务器的完美集群方案

{% asset_img 三台服务器的完美集群方案.jpg image %}

即满足高可用，又满足负载均衡

| --     | 服务端口  | 管理端口 | 存储                                     | 网络连接器         | 用途      |
| ------ | ----- | ---- | -------------------------------------- | ------------- | ------- |
| Node-A | 61616 | 8161 | -                                      | Node-B、Node-C | 消费者     |
| Node-B | 61617 | 8162 | /share_file/kahadb（如果是多台服务器，使用分布式文件系统） | Node-A        | 生产者，消费者 |
| Node-C | 61618 | 8163 | /share_file/kahadb                     | Node-A        | 生产者、消费者 |



### ActiveMQ集群配置方案

配置内容见上表

#### 环境配置

复制三个文件夹，进入`activemq-b/conf` 配置两个文件`activemq.xml  jetty.xml`

修改`activemq.xml`如下配置，

```xml
        <transportConnector name="openwire" uri="tcp://0.0.0.0:61618?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600"/>
<!--注释如下配置-->
       <!-- <transportConnector name="amqp" uri="amqp://0.0.0.0:5672?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600"/>
        <transportConnector name="stomp" uri="stomp://0.0.0.0:61613?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600"/>
        <transportConnector name="mqtt" uri="mqtt://0.0.0.0:1883?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600"/>
        <transportConnector name="ws" uri="ws://0.0.0.0:61614?maximumConnections=1000&amp;wireFormat.maxFrameSize=104857600"/>-->
    </transportConnectors>
<!--添加networkConnectors-->
    <networkConnectors>
            <networkConnector name="network_a" uri="static:(tcp://127.0.0.1:61616)" />
    </networkConnectors>
<!--activemq-a的配置-->
<networkConnectors>
                <networkConnector name="local_network" uri="static:(tcp://127.0.0.1:61617,tcp:/127.0.0.1:61618)"/>
        </networkConnectors>

```
添加共享文件夹地址，**只在activemq-b activemq-c中配置，activemq-a作为消息同步中心**

        <persistenceAdapter>
           <kahaDB directory="/opt/Dev/activeMQCluster/kahadb"/>
        </persistenceAdapter>
配置`jetty.xml`

修改管理页面端口号：`8162`

然后依次启动`activemq-a activemq-b activemq-c`

代码里面加上状态转移部分

```java
//在AppProducer下加入如下内容 A节点作为消费者，这里不配置
    private static final String  url = "failover:(tcp://127.0.0.1:61617,tcp://127.0.0.1:61618)?randomize=true";

//在AppConsumer下加入如下内容
    private static final String  url = "failover:(tcp://127.0.0.1:61616,tcp://127.0.0.1:61617,tcp://127.0.0.1:61618)?randomize=true";

```

