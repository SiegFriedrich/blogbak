---
layout: post
title: "SpringBoot 学习笔记 一"
date: 2017-12-29
description: "SpringBoot"
tag: 后台
---

### SpringBoot的介绍
Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。通过这种方式，Spring Boot致力于在蓬勃发展的快速应用开发领域(rapid application development)成为领导者

### SpringBoot的Annotation配置
- @Configuration和基础的@Bean
- @ComponentScan和测试
- @Bean标签属性详解
- 给@Bean注值
- @Import配置文件之间的引入
- XML和Annotation的混用
- 资源文件的导入
- @Profile和@ActiveProfile

### SpringBoot的使用

#### SpringBoot的HelloWorld小程序
---
1.新建一个maven的quickstart项目

2.修改pom文件如下

    <parent>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-parent</artifactId>
         <version>1.5.6.RELEASE</version>
     </parent>

     <dependencies>
         <dependency>
             <groupId>junit</groupId>
             <artifactId>junit</artifactId>
             <version>3.8.1</version>
             <scope>test</scope>
         </dependency>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-web</artifactId>
         </dependency>
     </dependencies>


3.新建一个Class,内容如下

    @Controller
    @SpringBootApplication
    public class HelloWorldController {

        @RequestMapping("hello")
        @ResponseBody
        public String hello(){
            return "Das ist zu schwer fuer mich manchmal!";
        }

        public static void main(String[] args) {
            SpringApplication.run(HelloWorldController.class,args);
        }
    }

4.双击main函数选择run 运行 OK!


#### SpringBoot的参数
---


#### SpringBoot中的日志
---
