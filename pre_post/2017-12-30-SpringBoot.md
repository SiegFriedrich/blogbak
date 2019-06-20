---
layout: post
title: "SpringBoot 学习笔记 二"
date: 2017-12-30
description: "SpringBoot"
tag: 后台
---

#### SpringBoot的WEB开发

- SpringBoot和FreeMarker原理

- web包的建立

-SpringBoot的quickstart的建立
  pom中引入jar包

    <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>1.5.6.RELEASE</version>
    </parent>

    <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-freemarker</artifactId>
      </dependency>
      ...
    </dependencies>

Controller类

    @Controller
    public class HelloController {
        @RequestMapping("hello")
        public String hello(Model model){
            model.addAttribute("msg","do not forget the future which you expected");
            return "page";
        }
    }

ApplicationConfig类

    @SpringBootApplication
    public class ApplicationConfig
    {
        public static void main( String[] args )
        {
            System.out.println( "Hello World!" );
            SpringApplication.run(ApplicationConfig.class,args);
        }
    }

建立后缀名为ftl的html文件,路径为main/resources/templates/page.ftl

文件内容为:

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    ${msg}
    </body>
    </html>

点击ApplicationConfig的主方法运行

- SpringBoot的异常处理

1,SpringBoot默认情况下，把所有错误都重新定位到/error这个处理路径上，由BasicErrorController类完成处理；

2，SpringBoot提供了默认的替换错误页面的路径：

1，静态错误页面默认结构：

    src/
        resources/
        public/
            error/
                404.html
                401.html
                5xx.html




2，也可以使用模板页面(配合错误视图跳转controller)：

    src/
        resources/
        templates/
                5xx.ftl
                err.ftl
                ...

控制错误跳转的视图Controller

    @ControllerAdvice
    public class ErrControllerAdvice {

        @ExceptionHandler(Exception.class)
        public String error(Model model, Exception e){
            model.addAttribute("msg", e.getMessage());
            return "err";
        }
    }

该路径方式是通过ErrorMvcAutoConfiguration中的DefaultErrorViewResolver完成的.

注意: 该注解还可以接受指定异常 @ExceptionHandler(RuntimeException.class)

- SpringBoot&MyBatis的集成

第一步,集成MyBatis首先要集成DataSource

1.建立application.properties资源文件放入resources里面

    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql:///p2p
    jdbc.username=root
    jdbc.password=admin

2.添加数据库集成依赖(德鲁伊)

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.6</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>

2.(自动装配)集成德鲁伊的第二种方式(DataSourceProperties类里自动过滤以spring.datasource开头的数据,选取其后面的数据)

    @ConfigurationProperties(
        prefix = "spring.datasource"
    )
    public class DataSourceProperties implements BeanClassLoaderAware, EnvironmentAware, InitializingBean {
        private ClassLoader classLoader;
        private Environment environment;
        private String name = "testdb";

所以可以在ApplicationConfig类中不用先配置DataSource类
直接在application.properties文件中直接写以下内容

    #使用自动装配,可以在applicationConfig中不写DataSource

    spring.datasource.driverClassName=com.mysql.jdbc.Driver
    spring.datasource.url=jdbc:mysql:///p2p
    spring.datasource.username=root
    spring.datasource.password=admin
    spring.datasource.type=com.alibaba.druid.pool.DruidDataSource


第二步,集成MyBatis

1.导入依赖

    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>1.3.0</version>
    </dependency>

删除掉以下依赖(上面依赖包含以下依赖)

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>

在resources中添加 mybatis.cfg.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <settings>
            <setting name="lazyLoadingEnabled" value="true"/>
            <setting name="aggressiveLazyLoading" value="false" />
            <setting name="lazyLoadTriggerMethods" value="clone" />
        </settings>
    </configuration>

2.在application.properties中添加如下文件

    mybatis.config-location=classpath:mybatis.cfg.xml
    mybtais.mapper-locations=classpath:mapper/*Mapper.xml

3.在ApplicationConfig类中添加扫描注解(不扫描根部就不知道你的mapper)

    @SpringBootApplication
    @MapperScan(basePackages = "com.sieg.mapper")
    public class ApplicationConfig
    {
        public static void main( String[] args )
        {
            System.out.println( "Hello World!" );
            SpringApplication.run(ApplicationConfig.class,args);
        }
    }


- SpringBoot环境下集成事务的配置

以前需要在applicationContext.xml中配置以下数据

    <!--5. 配置事务增强 -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
      <tx:attributes>
        <tx:method name="get*" read-only="true" />
        <tx:method name="select*" read-only="true" />
        <tx:method name="list*" read-only="true" />
        <tx:method name="query*" read-only="true" />
        <tx:method name="*"  />
      </tx:attributes>
    </tx:advice>

如果在classpath中添加的是spring-boot-starter-jdbc,那么SpringBoot自动创建DataSourceTransactionManager


1.使用注解的方式配置事务

直接开启@EnableTransactionManagement注解；相当于在xml中配置<tx:annotation-driven/>

    @SpringBootApplication
    @MapperScan(basePackages="com.sieg.mapper")
    @EnableTransactionManagement
    public class App {
      @Bean
      public PlatformTransactionManager txManager(DataSource dataSource){
              return new DataSourceTransactionManager(dataSource);
      }

      public static void main(String[] args) {
    		SpringApplication.run(App.class, args);
       }
    }

2.使用xml方式

将事务相关配置抽取到XML中，使用importResource引入

1，引入aop

    <dependency>
    			<groupId>org.springframework.boot</groupId>
    			<artifactId>spring-boot-starter-aop</artifactId>
    </dependency>

2，application-tx.xml

    <tx:advice id="advice" transaction-manager="transactionManager">
  		<tx:attributes>
  			<tx:method name="list*" read-only="true" />
  			<tx:method name="get*" read-only="true" />
  			<tx:method name="query*" read-only="true" />
  			<tx:method name="*" />
  		</tx:attributes>
    </tx:advice>

	<aop:config>
		<aop:pointcut expression="execution(* com.sieg..service.*Service.*(..))"
			id="pointCut" />
		<aop:advisor advice-ref="advice" pointcut-ref="pointCut" />
	</aop:config>

3，引入配置

    @SpringBootApplication
    @MapperScan(basePackages = "com.sieg.mapper")
    @ImportResource(locations="classpath:application-tx.xml")
    public class App {
      @Bean
        public PlatformTransactionManager txManager(DataSource dataSource){
            return new DataSourceTransactionManager(dataSource);
        }
        public static void main( String[] args )
        {
            SpringApplication.run(ApplicationConfig.class,args);
        }
    }
