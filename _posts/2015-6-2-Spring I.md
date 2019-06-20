---
layout: post
title: "Spring 学习 I"
date: 2015-6-2
description: "Spring  框架"
tag: 后台 框架
---


### Spring介绍

![](\mygithub\siegfriedrich.github.io\images\spring\spring.jpg)

Spring，即泉眼，代表最好的水，意思就是Spring是最好的框架

在企业里面，如果使用轻量级JavaEE规范，MVC和持久化层的选择性是很多的，但是对于中间业务层的框架，一般都选择Spring

![](\mygithub\siegfriedrich.github.io\images\spring\spring-overview.png)


轻量级vs重量级

1，轻量级是相对于重量级而言的，轻量级一般就是非入侵性的、所依赖的东西非常少、资源占用非常少、部署简单等等，其实就是比较容易使用，而重量级正好相反

2，相比较传统的JavaEE项目，即真正意义上的JavaEE规范（比如EJB3.0；JPA；JMX；JMS；JNDI；JSF等），我们把能直接在Tomcat等符合Java Servlet规范的Web服务器上能直接运行的Java应用称为轻量级的JavaEE应用；一般就意味着以Spring为代表的一些事实上的标准开发框架；可以说，Spring就是运行在Web服务器中的Application Server

(其他技术 redis/ssh)

Spring的优点

	1.低侵入/低耦合（降低组件之间的耦合度,实现软件各层之间的解耦,面向接口编程）
	2.声明式事务管理
	3.方便集成其他框架
	4.降低JavaEE开发难度
	5.Spring框架中包括JavaEE 三层的每一层的解决方案 （一站式）

### core cantainer(IoC/DI/AOP)

#### IoC
- IoC：inverse of control，即控制反转；
简单的说，就是把对象的实例化工作交给容器来完成


	Spring的使用:

1,导入相关jar包(beans/core);

2,导入依赖包(common-logging)

3,配置XML文件(作用:告诉spring帮我管理哪些bean);


	1,在classpath下,application.xml/applicationContext.xml;

	2,<beans><bean id="" class="" /></beans>

4,使用容器:

	1,创建一个资源文件对象(ClasspathResource);
	2,创建一个BeanFactory(Spring的容器);创建一个基于XML的BeanFactory:XmlBeanFactory,传入XML配置文件资源对象;
	3,从容器中拿对象:
		1,getBean(Class):按照类型拿bean;
		2,getBean(String):按照名字拿bean;
		3,getBean(String,Class):按照名字和类型拿;(推荐)【类型需要是接口的类型】

注意:从容器里面拿对象一定要使用接口去拿对象,不能直接使用真实的实现类

	分析Spring的加载过程:
	  1,找到对应的配置文件（xml）;
	  2,加载配置文件;
	  3,解析所有的bean元素;识别id和class属性;
	  4,通过反射,Class.forName().newInstance()去创建一个这个类型的对象实例;
	  5,把id作为key,把实例作为value存到spring容器中;
	  6,getBean从容器中拿到创建好的对象的实例;

		总结:
		1,spring的本质其实就是把应该写在java中的代码移到了配置文件中;
		2,默认管理的bean需要一个无参的构造方法;
		3,重要:在spring的配置文件中配置的不是类型,而是bean的实例;


通过import元素引入配置文件(默认使用相对路径),Spring提供了两种前缀来辅助查找配置文件

	1.[classpath:]:代表从classpath开始寻找后面的资源文件;(推荐使用)
	2.[file]:代表使用文件系统给的方式寻找后面的文件(文件的完整路径)


#### DI (Dependency Injection: 依赖注入)

指Spring创建对象的过程中，将对象依赖属性（简单值，集合，对象）通过配置设值给该对象。

IoC和DI其实是同一个概念的不同角度描述，DI相对IoC而言，明确描述了“被注入对象依赖IoC容器配置依赖对象”。


		@setter
		public class Employee{
			private String name;
			private Integer age;
			private BigDecimal salary;
			private URL url;
		}

使用DI注入的方式放进Spring容器中

		<bean id="employee" class="com.sieg.domain.Employee">
			<property name="name" value="Sieg">
			<property name="age" value="18">
			<property name="salary" value="800">
			<property name="url" value="https://siegfriedrich.github.io">
	</bean>

* 以上是手动注入,以后使用注解注入的方式

#### bean的实例化时机

1,对于beanfactory来说,在容器启动的时候,不会去实例化bean,必须要等到使用bean的时候,才会去初始化(延迟实例化);

2,对于ApplicationContext来说,在容器启动的时候,就已经把所有的管理的bean实例化了;

3,对于ApplicationContext:

1.可以在bean元素配置lazy-init=true来让bean延迟实例化(默认情况下是false,即初始化就加载);

2.可以在beans元素配置default-lazy-init=true来让这个beans里面所有的bean延迟实例化;

对比:

1,延迟实例化,在系统启动的时候快,系统启动的时候占用的资源少;并且,如果在使用的时候再去加载对象,第一次使用的时候性能较低;因为不会在加载的时候去实例化对象,所以延迟实例化不能对实例化出来的对象做更多的功能;

2,非延迟实例化,在系统启动的时候,所有对象被实例化,启动速度慢,使用速度快,启动的时候占用较多系统资源;

选择:

1,对于系统资源较少的应用可以使用延迟实例化(移动应用);

2,对于web应用来说,一般选择迫切实例化;

3,在web应用里面,我们一般把比较耗时的事情放在系统启动的时候完成;

Scope

	singleton 单例(Spring中默认/推荐)
	prototype 原型(多例 Struts2 Action中90%以上)
	1,在spring中配置的bean默认情况下就是单例的;
	2,在WEB应用中,持久化层和业务层的对象一般都是单例的;
	3,在struts2 中每次请求的Action都是全新的，所以不能为单例

bean的初始化&销毁

比如DataSource,SessionFactory最终都需要关闭资源:在Bean销毁之前,都要调用close方法.

分析原理:如果bean的scope="prototype",那么容器只负责创建和初始化，它并不会被spring容器管理,而是交给用户自己调用.

bean的生命周期过程

	init-method
	destroy-method

在SomeBean这个类中有两个方法 init(), 和 destroy()

	<bean id="someBean" class="com.sieg.spring.SomeBean"
	  init-method="init" destroy-method="destroy">
	</bean>

注意:在数据创建与销毁的时候调用以上2个属性,尤其在分布式开发时,一定要写上,否则分布式中不会关心其数据的销毁.


##### 使用Spring容器创建druid数据源(db.properties)

推荐加上jdbc. 这样加载时也写上jdbc. 程序可以定位加载jdbc的对应数据后续的system-properties-mode="NEVER" 这句话不写也可以.



	jdbc.driverClassName=com.mysql.jdbc.Driver
	jdbc.url=jdbc:mysql:///CRM
	jdbc.username=root
	jdbc.password=admin


property placeholder (属性占位符)

	<!-- applicationContext头部 -->
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
			http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
			http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd>


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

xml:schema(约束文件)/dtd/xsd

bean的元素继承

	<bean id="baseDAO" abstract="true">
		<property name="dataSource" value="重复的数据可以提取出来,abstrac表示让spring容器不要创建该属性的值"
	</bean>

	<bean id="departmentDAO" class="com.sieg.service.impl.departmentServiceImpl" parent="baseDAO"/>
	<bean id="employeeDAO" class="com.sieg.service.impl.employeeServiceImpl" parent="baseDAO"/>
