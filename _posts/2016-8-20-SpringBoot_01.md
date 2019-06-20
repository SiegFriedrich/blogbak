---
layout: post
title: "SpringBoot 学习笔记 1"
date: 2016-8-20
description: "SpringBoot"
tag: 后台
---

### SpringBoot
1,什么是SpringBoot
Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。通过这种方式，Spring Boot致力于在蓬勃发展的快速应用开发领域(rapid application development)成为领导者。

2,为什么要用SpringBoot
可以快速配置样板工程

3,SpringBoot和之前的开发方式有什么区别
xml等容器配置可以使用Config类配置

4,要学SpringBoot 先学 Spring-JavaConfig,SpringBoot配置推荐使用注解的方式进行配置(Spring的java配置)

### SpringBoot中Bean的容器注入

1.AppConfig(相当于ApplicationContext.xml)

    @Configuration
    public class AppConfig {

        @Bean
        public SomeBean someBean(){
            SomeBean someBean= new SomeBean();
            someBean.setOtherBean(otherBean());
            return someBean;
        }

        @Bean
        public OtherBean otherBean(){
            return new OtherBean();
        }
    }

2.Bean I

    @Getter@Setter
    public class SomeBean {
        private OtherBean otherBean;
    }

3.Bean II

    public c

4.Test

    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes = AppConfig.class)
    public class SomeBeanTest {
        @Autowired
        private ApplicationContext applicationContext;

        @Test
        public void SomeBeanTest() throws Exception{
            SomeBean someBean = applicationContext.getBean(SomeBean.class);
            OtherBean otherBean = applicationContext.getBean(OtherBean.class);
            System.out.println(someBean);
            System.out.println(otherBean);
            System.out.println(someBean.getOtherBean());
        }
    }

5.控制台输出

    com.sieg._02_bean_di.SomeBean@35047d03
    com.sieg._02_bean_di.OtherBean@49b0b76
    com.sieg._02_bean_di.OtherBean@49b0b76

    Process finished with exit code 0

由此可见,SpringBoot是集合Spring,其中Bean默认是单例模式.

### @Import 配置文件的引用/xml和Anotation的混用

 * @Import
 使用@Import引入其他J配置类


     @Configuration
     @Import(OtherConfig.class)
     public class AppConfig {

         @Bean
         public SomeBean someBean(){
             SomeBean someBean= new SomeBean();
             return someBean;
         }
     }


-

    @Configuration
    public class OtherConfig {

        @Bean
        public OtherBean otherBean(){
            return new OtherBean();
        }
    }


 * @Importresource


 applicationContext,xml


     <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean id="otherBean" class="com.sieg._03_xml_anno.OtherBean"/>
    </beans>

AppConfig(使用@ImportResource导入xml文件,当配置aop时无法使用anno,所以导入xml方便)

    @Configuration
    @ImportResource("applicationContext.xml")
    public class AppConfig {

        @Bean
        public SomeBean someBean(){
            SomeBean someBean= new SomeBean();
            return someBean;
        }
    }

### 引入properties 文件

1.db.properties文件

    username=Sieg
    password=666666

2.MyDataSource 类

    @Getter@Setter@ToString
    public class MyDataSource {
        private String username;
        private String password;
    }

3.AppConfig 类 (方式 一)

    @Configuration
    @PropertySource("classpath:db.properties")
    public class AppConfig {

        @Value("${username}")
        private String username;
        @Value("${password}")
        private String password;

        @Bean
        public MyDataSource myDataSource(){
            MyDataSource myDataSource = new MyDataSource();
            myDataSource.setUsername(this.username);
            myDataSource.setPassword(this.password);
            return myDataSource;
        }

        @Bean
        public static PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer(){
            return  new PropertySourcesPlaceholderConfigurer();
        }
    }

方式 二

    @Configuration
    @PropertySource("classpath:db.properties")
    public class AppConfig {

        @Bean
        public MyDataSource myDataSource(Environment environment){
            MyDataSource myDataSource = new MyDataSource();
            myDataSource.setUsername(environment.getProperty("username"));
            myDataSource.setPassword(environment.getProperty("password"));
            return myDataSource;
        }

    }

4.test测试类

    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes = AppConfig.class)
    public class SomeBeanTest {
        @Autowired
        private ApplicationContext applicationContext;

        @Test
        public void SomeBeanTest() throws Exception{
            MyDataSource myDataSource = applicationContext.getBean(MyDataSource.class);
            System.out.println(myDataSource);
        }
    }

5.控制台输出结果

>MyDataSource(username=Sieg, password=666666)

#### @Profile 和 ActiveProfile

dev.properties

>dev.username=dev.Sieg
>dev.password=dev.666666

test.properties

>test.username=test.Sieg
>test.password=test.666666

AppConfig 类

    @Configuration
    @Import(value = {DevAppConfig.class,TestAppConfig.class})
    public class AppConfig {
    }

DevAppConfig

    @Configuration
    @PropertySource("classpath:dev_db.properties")
    @Profile("dev")
    public class DevAppConfig {

        @Bean
        public MyDataSource myDataSource(Environment environment){
            MyDataSource myDataSource = new MyDataSource();
            myDataSource.setUsername(environment.getProperty("dev.username"));
            myDataSource.setPassword(environment.getProperty("dev.password"));
            return myDataSource;
        }
    }

TestAppConfig

    @Configuration
    @PropertySource("classpath:test_db.properties")
    @Profile("test")
    public class TestAppConfig {

        @Bean
        public MyDataSource myDataSource(Environment environment){
            MyDataSource myDataSource = new MyDataSource();
            myDataSource.setUsername(environment.getProperty("test.username"));
            myDataSource.setPassword(environment.getProperty("test.password"));
            return myDataSource;
        }
    }

MyDataSource 类

    @Getter@Setter@ToString
    public class MyDataSource {
        private String username;
        private String password;
    }


test测试类

    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes = AppConfig.class)
    //取不同的测试配置类名称,得到不同的结果
    @ActiveProfiles("test")/@ActiveProfiles("dev")
    public class SomeBeanTest {
        @Autowired
        private ApplicationContext applicationContext;

        @Test
        public void SomeBeanTest() throws Exception{
            MyDataSource myDataSource = applicationContext.getBean(MyDataSource.class);
            System.out.println(myDataSource);
        }
    }

控制台输出结果

@ActiveProfiles("test")
>MyDataSource(username=test.Sieg, password=test.666666)

@ActiveProfiles("dev")
>MyDataSource(username=dev.Sieg, password=dev.666666)
