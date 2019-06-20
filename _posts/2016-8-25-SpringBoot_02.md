---
layout: post
title: "SpringBoot 学习笔记 2"
date: 2016-8-25
description: "SpringBoot"
tag: 后台
---

### 打包方式 pom/jar/war

pom 作为pom管理
jar java普通的打包方式
war web工程的打包方式

    <dependencyManagement>用来管理jar包的版本,但是子项目不会继承,必须自己在pom文件里调用
    <dependencies>会默认让子工程集成父工程中的项目

* 在创建parent 项目的时候,idea 不需要选择具体的maven工程,直接点击next就可以了

* Spring-Boot-starter-web 引入web相关的jar包(springmvc相关的jar)

* maven中的instal命令会将当前工程打包并放入maven库当中

* resources中 application.properties 是SpringBoot默认的配置文件,所以名称别写错了,如果我要引用 application-web.properties,则要在App类中配置@PropertySource("classpath:application-web.properties")

* resources中
|--static //放置静态文件
|--tempalates//放置模板文件
|--application.properties//配置文件
