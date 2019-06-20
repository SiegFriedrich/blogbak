---
layout: post
title: "Spring 学习 III"
date: 2015-6-7
description: "Spring  框架"
tag: 后台 框架
---

### Spring的JDBC和ORM框架
* Spring支持JDBC(使用Spring之后,我们需要做的事情)
	- 定义连接参数
	- 定义sql语句
	- 定义sql语句的参数&参数值
	- 处理迭代结果  


* SpringTemplate使用
* Springjdbc的DQL
* Springjdbc的DML
* SpringjdbcDAOSupport

### 事务
* 引出事务
* 流水账分析
* Spring对事务的支持

#### Spring的事务管理主要包括3个接口：

1.TransactionDefinition：封装事务的隔离级别和超时时间，是否为只读事务和事务的隔离级别和传播规则等事务属性，可通过XML配置具体信息。

2.PlatformTransactionManager：根据TransactionDefinition提供的事务属性配置信息，创建事务。

3.TransactionStatus：封装了事务的具体运行状态。比如，是否是新开启事务，是否已经提交事务，设置当前事务为rollback-only等。

#### Spring支持编程式事务管理和声明式事务管理:

1.编程式事务管理：事务和业务代码耦合度太高。(手动式事务,在代码里修改)
2.声明式事务管理：侵入性小，把事务从业务代码中抽离出来，提供维护性。(AOP)

JDBC/MyBaits使用DataSourceTransactionManageer作为事务管理.

###### 申明事务基于xml的配置

- 事务传播机制(propagation)6种
我们常使用PROPAGATION_REQUIRED
其他包括PROPAGATION_SUPPORTS/PROPAGATION_MANDATORY/PROPAGATION_REQUIRES_NEW
PROPAGATION_NOT_SUPPORTED/PROPAGATION_NEVER/PROPAGATION_NESTED

一般情况下,我们使用的是PROPAGATION_REQUIRED

	<!--1. 引入数据库配置文件 -->
	<!-- 由于系统默认加载系统文件, 所以系统资源模式使用NEVER -->
	<context:property-placeholder location="classpath:jdbc.properties" system-properties-mode="NEVER" />

	<!--2. 配置数据库连接池 -->
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
		<property name="url" value="${jdbc.url}"/>
		<property name="username" value="${jdbc.username}"/>
		<property name="password" value="${jdbc.password}"/>
		<property name="driverClassName" value="${jdbc.driverClassName}"/>
	</bean>

	<!--配置实务 3w-->
	<!--what 做什么增强-->
	<bean id="txManager" class="org.springframework.jdk.dataSource.DataSourceTransactionManager">
		<!--确定给哪一个连接池做实务管理-->
		<property name="dataSource" ref="dataSource"/>
	</bean>

	<!--什么时机做增强-->
	<tx:advice id="txAdvisor" transaction-manager="txManager">
		<!--给哪一些方法做增强(更细致配置)-->
		<tx:attributes>
			<tx:method name="具体实务方法名"/>
		</tx:attributes>
	</tx>

	<!--where: 在哪里做增强-->
	<aop:config>
		<aop:pointcut expression="execution(* com.sieg.demo.service.*.*(..))" id="pc"/>
		<!--切面: 将what where when 链接起来-->
		<aop:advisor advice-ref="txAdvisor" pointcut-ref="pc"/>
	</aop:config>

	</bean>


###### 声明事务基于注解配置
1.在xml文件中开启事务的解析器

	<tx:annotation-driven transaction-manager="txManager"/>

2.在Servic层引入事务标签@Transactional
