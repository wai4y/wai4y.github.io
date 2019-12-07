---
title: ActiveMQ在Java中的实战使用.md
date: 2018-03-12 23:21:52
categories:
- 2018
- 03月
tags:
- java
---

之前学习了一下ActiveMQ的相关知识，正好项目中使用到了，现将使用过程梳理如下。

___
配置文件有两个： activemq.properties和spring-activemq.xml
**这里使用的是队列模式**

<!--more-->

**activemq.properties**

```
#服务器地址
amq.brokerURL=tcp://127.0.0.1:61616
amq.userName=admin
amq.password=admin

#定义queue
send.queue.destination.pdms=syncdataqueue.dev

#定义product-dev-queue 开发环境
listener.queue.destination.dev=synchrodata.queue.dev
#监听
listener.queue.ref.dev=synchrodataReceiverDev

# 定义product-sit-queuesit  测试环境
listener.queue.destination.sit=synchrodata.pdms.product.queue.sit
#监听
listener.queue.ref.sit=synchrodataReceiverSit
```
---
**spring-activemq.xmlspring-activemq.xml**
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:amq="http://activemq.apache.org/schema/core"
    xmlns:jms="http://www.springframework.org/schema/jms"
    xsi:schemaLocation="http://www.springframework.org/schema/beans   
        http://www.springframework.org/schema/beans/spring-beans.xsd   
        http://www.springframework.org/schema/context   
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/jms
        http://www.springframework.org/schema/jms/spring-jms.xsd
        http://activemq.apache.org/schema/core
        http://activemq.apache.org/schema/core/activemq-core.xsd">
        
<!-- ActiveMQ 连接工厂 -->
    <!-- 真正可以产生Connection的ConnectionFactory，由对应的 JMS服务厂商提供-->
    <!-- 如果连接网络：tcp://ip:61616；未连接网络：tcp://localhost:61616 以及用户名，密码-->
    <amq:connectionFactory id="amqConnectionFactory"
        brokerURL="${amq.brokerURL}"     userName="${amq.userName}" password="${amq.password}"  />


    <!-- Spring Caching连接工厂 -->
    <!-- Spring用于管理真正的ConnectionFactory的ConnectionFactory -->  
    <bean id="connectionFactory" class="org.springframework.jms.connection.CachingConnectionFactory">
        <!-- 目标ConnectionFactory对应真实的可以产生JMS Connection的ConnectionFactory -->  
        <property name="targetConnectionFactory" ref="amqConnectionFactory"></property>
        <!-- Session缓存数量 -->
        <property name="sessionCacheSize" value="100" />
    </bean>


    <!-- Spring JmsTemplate 的消息生产者 start-->

    <!-- 定义JmsTemplate的Queue类型 -->
    <bean id="jmsQueueTemplate" class="org.springframework.jms.core.JmsTemplate">
        <!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->  
        <constructor-arg ref="connectionFactory" />
        <!-- 队列模式 -->
        <property name="pubSubDomain" value="false" />
    </bean>

    <!--Spring JmsTemplate 的消息生产者 end-->
    
    <!-- 定义Queue监听器 -->
     <jms:listener-container destination-type="queue" container-type="default" connection-factory="connectionFactory" acknowledge="auto">
        <jms:listener destination="${listener.queue.destination.dev}" ref="${listener.queue.ref.dev}"/>
```
---
**具体的实现类**
**AbstractMqService.java**
```
package com.activemq.service;

import org.slf4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.util.StringUtils;
import com.activemq.service.QueueSender;

/**
 * 
 * <pre>发送mq抽象类 </pre>
 * 
 */
@Service
public abstract class AbstractMqService {

    @Log
    protected Logger logger;
    
    @Autowired
    private QueueSender queueSender;

    /**
     * 发送mq队列
     * @param queueTarget
     * @param messageJson
     */
    protected void sendQueue(String queueTarget, String messageJson) {
        logger.info("发送mq地址：【{}】 , mq报文：【{}】" , queueTarget , messageJson);
        
        if(!StringUtils.isEmpty(queueTarget) && !StringUtils.isEmpty(messageJson)  ){
            
            //向指定的地址发送报文
            this.queueSender.send(queueTarget , messageJson);
        }
    }
    
    
    /**
     * 发送消息到主题
     * Topic主题 ：放入一个消息，所有订阅者都会收到 
     * 这个是主题目的地是一对多的
     * @param message
     * @return String
     */
    protected void sendTopic(String topicTarget, String messageJson){
          logger.info("发送mq地址：【{}】 , mq报文：【{}】" , topicTarget , messageJson);
          
          if(!StringUtils.isEmpty(topicTarget) && !StringUtils.isEmpty(messageJson)  ){

              //向指定的地址发送报文
              this.topicSender.send(topicTarget , messageJson);
          }
        
    }
}
```
---
**AbstractThreadPoolService.java**
```
package com.activemq.service;

import java.util.Observable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.FutureTask;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import org.slf4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.util.StringUtils;


/**
 * 线程池
 * 每一个继承者都会创建一个线程池
 * @author Administrator
 *
 */
@Service
public abstract class AbstractThreadPoolService<T> extends Observable  {

    @Log
    protected Logger logger;
    
    /**最大等待循环次数*/
    private int MAXWAITTIMES = 300 ;
    
    /**线程池数量*/
    private int THREADNUM = 5; 
    
    /**线程池*/
    private ExecutorService threadPool;  
    
    
    @PostConstruct  
    public void init(){  
        this.threadPool = Executors.newFixedThreadPool(THREADNUM);  
        logger.info("线程池初始化完成……");  
    } 
    
    @PreDestroy  
    public void destroy(){  
        threadPool.shutdown();  
        logger.info("线程池销毁完成……");  
    } 
    
    /**
     * 转业务操作
     * @param futureTask
     */
    private void transferOperation(FutureTask<T> futureTask){
        
        try {
            
            int i = 1 ;
            while(!futureTask.isDone() && i <= MAXWAITTIMES){
                //保存到日志表未处理完成等待50毫秒,最大循环300次（15s）
                Thread.sleep(50);
                i++ ;
            }
                
            if(futureTask.isDone() && !StringUtils.isEmpty(futureTask.get())){
              
                T finPMqlogDto = futureTask.get() ; 
                
                super.setChanged();
                super.notifyObservers(finPMqlogDto);
            }
            
        } catch (InterruptedException e) {
            e.printStackTrace();
            logger.error("InterruptedException:{}", e.getMessage());
        } catch (ExecutionException e) {
            e.printStackTrace();
            logger.error("ExecutionException:{}", e.getMessage());
        }
    }
    
    /**
     * 设置观察者服务
     */
    private void setObserverService() {
        IMqLogTransService mqLogTransService = this.getMqLogTransService();
        if(!StringUtils.isEmpty(mqLogTransService)){
            //添加观察者
            this.addObserver(mqLogTransService);
        }
    }

    /**
     * 创建多个有返回值的任务  
     */
    public abstract FutureTask<T> createNewFutureTask(final String requestJson);
    
    /**
     * 提交一个任务处理开始
     * @param futureTask
     */
    public void submitTask(final String requestJson){
        
        FutureTask<T> futureTask = this.createNewFutureTask(requestJson);
        threadPool.submit(futureTask);  
    }
    
    /**
     * 需要转业务操作
     * @param requestJson
     */
    public void submitTaskWithTransfer(final String requestJson){
        
        FutureTask<T> futureTask = this.createNewFutureTask(requestJson);
        threadPool.submit(futureTask);  
        
        this.setObserverService();
        
        this.transferOperation(futureTask);
        
    }
    
}
```
**QueueSender.java**
```
package com.activemq.service;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.Session;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.jms.core.MessageCreator;
import org.springframework.stereotype.Component;

/**
 * @description  队列消息生产者，发送消息到队列
 */
@Component("queueSender")
public class QueueSender {
    
    @Value("${send.queue.destination.pdms}")
    private String queueTarget ;
    
    @Autowired
    @Qualifier("jmsQueueTemplate")
    private JmsTemplate jmsTemplate;
    
    /**
     * 发送一条消息到指定的队列（目标）
     * @param queueName 队列名称
     * @param message 消息内容
     */
    public void send(String queueName,final String message){
            jmsTemplate.send(queueName, new MessageCreator() {
            public Message createMessage(Session session) throws JMSException {
                return session.createTextMessage(message);
            }
        });
    }
    
    /**
     * 发送一条消息到指定的队列（目标）
     * @param message 消息内容
     */
    public void send(final String message){
        jmsTemplate.send(queueTarget, new MessageCreator() {
            public Message createMessage(Session session) throws JMSException {
                return session.createTextMessage(message);
            }
        });
    }
    
}
```
**SyncProducer.java**
```

package com.activemq.service;

import com.activemq.service.AbstractMqService;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;



@Service
public class QueueSynchrodataProducer extends AbstractMqService {

    @Value("${listener.queue.destination.dev}")
    private String devQueueTarget ;

    @Value("${listener.queue.destination.sit}")
    private String sitQueueTarget ;


    /**
     * 发送到不同环境
     * @param messageJson
     * @return
     */
    public boolean syschrodata(String messageJson){
         this.sendQueue(devQueueTarget, messageJson);
         this.sendQueue(sitQueueTarget, messageJson);
         return true;
     } 
}

```
---
**SynchrodataReceiverDev.java**
这是开发环境的消费操作，测试环境的同理
```
package com.activemq.service;

import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.TextMessage;


import org.slf4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.util.StringUtils;

import com.alibaba.fastjson.JSON;
import com.activemq.service.AbstractThreadPoolService;
/**
* 
*/
@Service
public class SynchrodataReceiver extends AbstractThreadPoolService<String> implements MessageListener {
    
      @Log
      protected Logger logger;

      @Autowired
      MqHelper mqHelper;

    /* 
     * @see javax.jms.MessageListener#onMessage(javax.jms.Message)
     * @param message
     */
    @Override
    public void onMessage(Message message) {
        try {
                //监听消息并处理
                long beginTime = System.currentTimeMillis();
                TextMessage textMsg = (TextMessage) message;
            
                
                this.submitTaskWithTransfer(textMsg.getText());
                long endTime = System.currentTimeMillis();
                
            
        } catch (JMSException e) {
            logger.error("JMSException:{}", e);
        }

    }


    /* 
     * @param requestJson
     * @return
     */
    @Override
    public FutureTask<String> createNewFutureTask(final String requestJson) {
          // 创建多个有返回值的任务  
        FutureTask<String> futureTask = new FutureTask<String>(new Callable<String>() {  
            @SuppressWarnings("unchecked")
            @Override  
            public String call() throws Exception {  
                try{
            
                    
                    //详细处理逻辑
                        mqHelper.disposeData(requestJson);
                    
                    //mq订阅到消息后， 本地处理消息
                    logger.info("接收的消息内容:{"+requestJson+"}");
                }catch(Exception e){
                    logger.error("消息推送处理异常：{}" , e）;
                }
                logger.info("消息推送处理结束" );
                return "";  
            }  
        });  
        return futureTask ;
    }
}
```
---
**MqHelper.java**
这里处理业务逻辑
```
package com.activemq.service;

@Component
public class MqHelper implements Runnable {
   

    private final SyncProducer producer;

    @Autowired
    public ProductMqHelper(SyncProducer producer) {
        this.producer = producer;
       }
    
    private String str ;
    
    public String getStr(){
       return this.str;
    }    
    public void setStr(String str){
        this.str = str;
    }
    //发送消息
     public boolean sendData(String str) {
       //发送消息
       producer.syschrodata(str);
                return false;
     }
     
     
    @Override
    public void run() {
       //要发送的消息体
       String str = "sending message";
       sendData(str);
    }
    
    //接收到消息后的数据处理逻辑,主要在这里处理消费的消息
     public void disposeData(String jsonStr){
     //数据处理
     //xxxxxxxx
     }

}
```
---
**发送消息触发点**
在其他业务类中，使用ExecutorService类来启动线程，发送消息到队列
```
ExecutorService cachedThreadPool = cachedThreadPool = Executors.newCachedThreadPool();

cachedThreadPool.execute(mqHelper.setStr("sending message"));
```
---
以上就是ActiveMQ在实际业务中的使用场景。


