---
layout: post
title: "SpringMVC 学习"
date: 2015-6-10
description: "SpringMVC  框架"
tag: 后台 框架
---
### Spring MVC
##### MVC模型
是一种架构型的模式，本身不引入新功能，只是帮助我们将开发的结构组织的更加合理，使展示与模型分离、流程控制逻辑、业务逻辑调用与展示逻辑分离------>责任分离。
##### 什么是Spring MVC？
MVC框架，它解决WEB开发中常见的问题(参数接收、文件上传、表单验证、国际化、等等)，而且使用简单，与Spring无缝集成。
Spring3.0 后全面超越 Struts2，成为最优秀的 MVC 框架 （更安全，性能更好，更简单）。
支持 RESTful风格的 URL 请求 。
采用了松散耦合可插拔组件结构，比其他 MVC 框架更具扩展性和灵活性。
##### SpringMVC和Struts2对比
①. Spring MVC 的入口是 Servlet, 而 Struts2 是 Filter.

②. Spring MVC 会稍微比 Struts2 快些. Spring MVC 是基于方法设计,  而 Sturts2 是基于类,  每次发一次请求都会实例一个 Action.

③. Spring MVC 使用更加简洁,  开发效率Spring MVC确实比 struts2 高:支持 JSR303, 处理AJAX请求更方便.

④. Struts2 的 OGNL 表达式使页面的开发效率相比Spring MVC 更高些.

##### Spring MVC 执行流程
	01、用户发送出请求到前端控制器DispatcherServlet
	02、DispatcherServlet收到请求调用HandlerMapping（处理器映射器）
	03、HandlerMapping找到具体的处理器(可查找xml配置或注解配置)，
		生成处理器对象及处理器拦截器(如果有)，再一起返回给DispatcherServlet
	04、DispatcherServlet调用HandlerAdapter（处理器适配器）
	05、HandlerAdapter经过适配调用具体的处理器（Handler/Controller）
	06、Controller执行完成返回ModelAndView对象
	07、HandlerAdapter将Controller执行结果ModelAndView返回给DispatcherServlet
	08、DispatcherServlet将ModelAndView传给ViewReslover（视图解析器）
	09、ViewReslover解析后返回具体View（视图）
	10、DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）
	11、DispatcherServlet响应用户

##### 入门程序
1.搭建Web项目

2.导入jar包


	<!-- 项目编码 -->
	<properties>
		 <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		 <org.springframework.version>4.2.4.RELEASE</org.springframework.version>
		 <org.mybatis.version>3.2.3</org.mybatis.version>
	</properties>

	<!--jar包依赖-->
	<dependencies>
	      <!-- 1):junit -->
	      <dependency>
	          <groupId>junit</groupId>
	          <artifactId>junit</artifactId>
	          <version>4.12</version>
	          <scope>test</scope>
	      </dependency>
	      <!-- 2):lombok工具 -->
	      <dependency>
	          <groupId>org.projectlombok</groupId>
	          <artifactId>lombok</artifactId>
	          <version>1.16.6</version>
	      </dependency>
	      <!-- 3):MySQL驱动包 -->
	      <dependency>
	          <groupId>mysql</groupId>
	          <artifactId>mysql-connector-java</artifactId>
	          <version>5.1.21</version>
	          <scope>runtime</scope>
	      </dependency>
	      <!-- 4):druid包 -->
	      <dependency>
	          <groupId>com.alibaba</groupId>
	          <artifactId>druid</artifactId>
	          <version>1.0.15</version>
	      </dependency>
	      <!-- 5):servlet-api -->
	      <dependency>
	          <groupId>javax.servlet</groupId>
	          <artifactId>javax.servlet-api</artifactId>
	          <version>3.0.1</version>
	          <scope>provided</scope>
	      </dependency>
	      <!-- 6):图片缩略工具 -->
	      <dependency>
	          <groupId>net.coobird</groupId>
	          <artifactId>thumbnailator</artifactId>
	          <version>0.4.8</version>
	      </dependency>
	      <!--7):日志-->
	      <dependency>
	          <groupId>log4j</groupId>
	          <artifactId>log4j</artifactId>
	          <version>1.2.12</version>
	      </dependency>
	      <dependency>
	          <groupId>org.slf4j</groupId>
	          <artifactId>slf4j-api</artifactId>
	          <version>1.7.20</version>
	      </dependency>
	      <dependency>
	          <groupId>org.slf4j</groupId>
	          <artifactId>slf4j-log4j12</artifactId>
	          <version>1.7.20</version>
	      </dependency>
	      <!-- 8):fastjson -->
	      <dependency>
	          <groupId>com.alibaba</groupId>
	          <artifactId>fastjson</artifactId>
	          <version>1.2.7</version>
	      </dependency>
	      <!-- 9):jackson -->
	      <dependency>
	          <groupId>com.fasterxml.jackson.core</groupId>
	          <artifactId>jackson-core</artifactId>
	          <version>2.5.0</version>
	      </dependency>
	      <dependency>
	          <groupId>com.fasterxml.jackson.core</groupId>
	          <artifactId>jackson-databind</artifactId>
	          <version>2.5.0</version>
	      </dependency>
	      <dependency>
	          <groupId>com.fasterxml.jackson.core</groupId>
	          <artifactId>jackson-annotations</artifactId>
	          <version>2.5.0</version>
	      </dependency>
	      <!--10):fileupload -->
	      <dependency>
	          <groupId>commons-fileupload</groupId>
	          <artifactId>commons-fileupload</artifactId>
	          <version>1.3.1</version>
	      </dependency>
	      <!--11):aspectjweaver -->
	      <dependency>
	          <groupId>org.aspectj</groupId>
	          <artifactId>aspectjweaver</artifactId>
	          <version>1.8.7</version>
	      </dependency>
	      <!--12):cglib-->
	      <dependency>
	          <groupId>cglib</groupId>
	          <artifactId>cglib</artifactId>
	          <version>3.2.2</version>
	      </dependency>
	      <dependency>
	          <groupId>jstl</groupId>
	          <artifactId>jstl</artifactId>
	          <version>1.2</version>
	      </dependency>
	      <dependency>
	          <groupId>taglibs</groupId>
	          <artifactId>standard</artifactId>
	          <version>1.1.2</version>
	      </dependency>
	      <!-- Spring框架 -->
	      <dependency>
	          <groupId>org.springframework</groupId>
	          <artifactId>spring-core</artifactId>
	          <version>${org.springframework.version}</version>
	      </dependency>
	      <dependency>
	          <groupId>org.springframework</groupId>
	          <artifactId>spring-jdbc</artifactId>
	          <version>${org.springframework.version}</version>
	      </dependency>
	      <dependency>
	          <groupId>org.springframework</groupId>
	          <artifactId>spring-test</artifactId>
	          <version>${org.springframework.version}</version>
	          <scope>test</scope>
	      </dependency>
	      <!--Spring MVC-->
	      <dependency>
	          <groupId>org.springframework</groupId>
	          <artifactId>spring-webmvc</artifactId>
	          <version>${org.springframework.version}</version>
	      </dependency>
	      <!-- MyBatis -->
	      <dependency>
	          <groupId>org.mybatis</groupId>
	          <artifactId>mybatis</artifactId>
	          <version>${org.mybatis.version}</version>
	      </dependency>
	      <!-- mybatis和spring集成包 -->
	      <dependency>
	          <groupId>org.mybatis</groupId>
	          <artifactId>mybatis-spring</artifactId>
	          <version>1.2.3</version>
	      </dependency>
	      <dependency>
	          <groupId>junit</groupId>
	          <artifactId>junit</artifactId>
	          <version>4.12</version>
	      </dependency>
	      <dependency>
	          <groupId>org.springframework</groupId>
	          <artifactId>spring-test</artifactId>
	          <version>4.2.4.RELEASE</version>
	      </dependency>
		</dependencies>

3.web.xml配置前端控制器

	<?xml version="1.0" encoding="UTF-8"?>
	<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	         xmlns="http://java.sun.com/xml/ns/javaee"
	         xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
	         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
	         http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
	         id="WebApp_ID" version="3.0">

	    <!--配置SpringMVC 前端控制器-->
	  <servlet>
	    <servlet-name>springMVC</servlet-name>
	    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
			<!--引入mvc.xml-->
	    <init-param>
	      <param-name>contextConfigLocation</param-name>
	      <param-value>classpath:mvc.xml</param-value>
	    </init-param>
	    <!--表示服务器启动时,就初始化Spring容器-->
	    <load-on-startup>1</load-on-startup>
	  </servlet>

	  <servlet-mapping>
	    <servlet-name>springMVC</servlet-name>
	    <url-pattern>/</url-pattern>
	  </servlet-mapping>

	</web-app>

4.mvc.xml配置Spring的处理映射器,适配器,解析器,配置处理器

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xsi:schemaLocation="
	    	http://www.springframework.org/schema/beans
	        http://www.springframework.org/schema/beans/spring-beans.xsd">

	    <!--1:处理器映射器-->
	    <!--处理器映射器将处理器(handler)的name作为URL查找-->
	    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>

		  <!--2:处理器适配器-->
	    <!--
	        ①:所有的适配器都需要实现于HandlerAdapter接口
	        ②:要求所有的处理器需要实现于Controller接口
	    -->
	    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>

			<!--3:视图解析器-->
	    <!--缺省使用JSTL-->
	    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>

			<!--4:自定义处理器 表示在地址栏输入hello之后对应的映射路径-->
	    <bean id="/hello" class="com.sieg.demo.controller.HelloController"/>
	</beans>


##### 涉及组件分析：

1、前端控制器DispatcherServlet（不需要程序员开发）,由框架提供，在web.xml中配置。
作用：接收请求，响应结果，相当于转发器，中央处理器。

2、处理器映射器HandlerMapping(不需要程序员开发),由框架提供。
作用：根据请求的url查找Handler(处理器/Controller)，可以通过XML和注解方式来映射。

3、处理器适配器HandlerAdapter(不需要程序员开发),由框架提供。
作用：按照特定规则（HandlerAdapter要求的规则）去执行Handler。

4、处理器Handler(也称之为Controller，需要工程师开发)
注意：编写Handler时按照HandlerAdapter的要求去做，这样适配器才可以去正确执行Handler。
作用：接受用户请求信息，调用业务方法处理请求，也称之为后端控制器。

5、视图解析器ViewResolver(不需要程序员开发),由框架提供
作用：进行视图解析，把逻辑视图名解析成真正的物理视图。
SpringMVC框架支持多种View视图技术，包括：jstlView、freemarkerView、pdfView等。

6、视图View(需要工程师开发)
作用：把数据展现给用户的页面
View是一个接口，实现类支持不同的View技术（jsp、freemarker等）

##### Spring对静态资源的访问
Tomcat默认是对所有/开头的资源进行访问处理的,但是如果在web.xml中做如下映射处理

	<servlet-mapping>
	  <servlet-name>springMVC</servlet-name>
	  <url-pattern>/</url-pattern>
	</servlet-mapping>

这样会让Spring覆盖掉Tomcat的默认处理路径

解决方法:

	1:使用springmvc框架自己静态资源处理器
		web.xml : <url-pattern>/</url-pattern>
		mvc.xml:<mvc:default-servlet-handler/>
	2:使用tomcat的(推荐)
		web.xml : <url-pattern>*.do</url-pattern>

*.do表示 只有.do后缀名的网页请求才会进入Spring的Dispatcher处理器进行处理,其他的不处理.

 ##### /和/*的区别

/  会匹配url请求/index等 ,也会匹配静态资源*.js,*.html等, 不会匹配*.jsp文件。

/* 会匹配url请求/index等 ,也会匹配静态资源*.js,*.html等, 会匹配*.jsp文件。如果使用,JSP直接响应,没有渲染.

##### 使用注解
mvc.xml

	<!--配置IoC注解解析器-->
	<context:component-scan base-package="com.sieg.demo"/>

	<!--配置Spring驱动
			包含:处理器映射器/处理器适配器/视图解析器/等其他额外功能
	-->
	<mvc:annotation-driven/>

	<!--覆盖默认的视图解析器-->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
			<property name="prefix" value="/WEB-INF/views/"/>
			<property name="suffix" value=".jsp"/>
	</bean>

##### 响应传值的方式

多使用以下这种方式,直接使用Model设置值,返回String类型(视图地址)

	@Controller
	public class HelloController{
	    @RequestMapping("/hello")
	    public String hello(Model model) throws Exception{
	        model.addAttribute("msg","新たな世界！ようこそ");
	        return "hello";
	    }
	}

### 拦截器

1.创建拦截器类
