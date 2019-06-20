---
layout: post
title: "ActiveMQ 消息队列"
date: 2015-10-3
description: "消息中间件"
tag: 消息中间件
---

## 消息中间件
存在高并发的业务
<如果使用乐观锁,同时只有一个人能完成操作,其他人会失败,体验效果较差 >

耗时比较久的业务
例如:生成订单

需要解耦出来的业务
消息中心可以并发的接受消息
消息是以队列的方式去发送的,即以队列的方式区处理,
消息中间件存储消息安全可靠不会无辜丢失数据
前台消息发送之后,一旦消息中心接受,则表示发送成功

    1.使用Redis，使用循环，确保商品个数(push/pop方法)
    2.使用ActiveMQ，确保有一定的个数的消息

##### 什么是消息中间件：

消息中间件MOM（Message Oriented Middleware）是在分布式环境中，两个或多个独立运行的系统之间，提供消息通讯作用的中介。
消息中间件的作用：
把分布式应用中的各个子系统之间服务的调用以消息通讯的方式交互
消息中间件的特点：

    1、消息异步接收：消息发送者不需要等待消息接收着的响应，提高整个应用程序的性能
    2、消息可靠接收：消息发送出去后保存在一个中间容器内，只有消息接收者收到消息后才删除消息
    3、消息队列接收：消息以队列的形式接收,一个一个排队处理

比较流行的消息中间件有哪些：
收费：IBM MQSeries，BEA WebLogic JMS Server，Oracle AQ
开源：ActiveMQ，RabbitMQ,RocketMQ，Kafka

##### JMS

他是一套接口(像JDBC)
面向消息中间件的接口(ActiveMQ,RabbitMQ,...)
所有的消息中间件实现JMS接口规范

_kafka没有实现JMS(但处理能力最强)_

    点对点
    1.每一个消息只有一个消费者
    2.没有事件关联性

    发布/订阅
    1.每个消息有多个消费者
    2.每个消费者只能拿到的自他订阅开始的消息(没有时间关联性,只要有一个消费者拿到消息,中间件就会消除消息,如果其中一个消费者宕机也不管)
    * topic消息的持久订阅?
JTA

ActiveMQ安装使用 &  HelloWorld

下载,解压,启动/bin/activemq.bat

SpringBoot整合ActiveMQ

SpringBoot的dependecies中包含了spring-boot-starter-activemq
需要的server导入activemq的jar包


application.properties中配置

    在springboot应用程序中的主配置文件application.properties文件中添加如下ActiveMQ的属性配置
    #activeMQ部署地址
    spring.activemq.broker-url=tcp://127.0.0.1:61616
    #activeMQ访问用户名
    spring.activemq.user=admin
    #activeMQ访问密码
    spring.activemq.password=admin

类:JmsTemplate,Destination
方法:convertAndSend



分布式事物
