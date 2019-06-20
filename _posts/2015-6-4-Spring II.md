---
layout: post
title: "Spring 学习 II"
date: 2015-6-4
description: "Spring  框架"
tag: 后台 框架
---

### Spring的注解(bean的装配标签)

#### @Autowired标签

1.通过@Autowired标签可以让Spring自动把属性需要的对象从Spring容器中找出来,并注入给该属性.

2.@Autowired标签贴在字段或者setter方法上。

3.@Autowired可以同时为一个属性注入多个对象。

4.使用@Autowired标签可以注入Spring内置的重要对象，比如BeanFactory,ApplicationContext。

	@RunWith(SpringJUnit4ClassRunner.class)
	@ContextConfiguration
	public class SpringTest {
	    @Autowired
	    private ApplicationContext ctx;
	}

5.默认情况下@Autowired标签必须要能找到对应的对象，否则报错

6.@Autowired找bean的方式：

	1）,首先按照依赖对象的类型找，如果找到则使用setter方法或者字段直接注入；
	2）,如果在Spring上下文中找到多个匹配的类型，再按照名字去找，如果没有匹配则报错；
	3）,可以通过使用@Qualifier("otherBean")标签来规定依赖对象按照bean的id+类型去找；



#### @Resource标签:


  1,@Resource标签是JavaEE规范的标签;

  2,@Resource标签也可以作用于字段或者setter方法;, 效果是跟autowired是一样的

  3,也可以使用@Resource标签注入一些spring内置的重要对象,比如BeanFactory.ApplicationContext;

  4,@Resource必须要求有匹配的对象;

  5,<context:annotation-config/>既引入了@Autowired标签的解析器,也引入了@Resource的解析器;

  6,@Resource标签找bean的方式:

		1),首先按照名字去找,如果找到,就使用setter或者字段注入;
		2),如果按照名字找不到,再按照类型去找,但如果找到多个匹配类型,报错;
		3),可以直接使用name属性指定bean的名称;但是,如果指定的name,就只能按照name去找,如果找不到,就不会再按照类型去找;

注意: @Autowired VS @Resource:

1,@Autowired:是Spring定义的标签,所以不太稳定,并且对象和spring框架关联(Spring对我们的代码有侵入)

2,@Resouce:是J2EE的规范,所以稳定,在J2EE规范容器中也能正常使用

* IoC注解

1.使用标签来完成IoC,就必须有IoC标签的解析器:
	使用context:component-scan来扫描spring需要管理的bean
	base-package就告诉spring,去哪些包及其子包里去扫描bean,如果有多个包需要被扫描;只需要用逗号隔开多个包即可

	<!--开启IoC注解扫描器  多个包可以用逗号隔开-->
	<context:component-scan base-package="com.sieg.oa.dao,com.sieg.service" />
	<!--开启DI注解解析程序-->
	<context:annotation-config/>

* 标注Bean的注解：@Component

默认情况,直接使用类的名字(首字母小写作为bean的名字),如果要修改bean的名称;直接使用value属性来重新定义bean的名称


	@Component("otherbean")
	public class OtherBean {}

使用@Component的限制:

  1),不能运用到静态工厂方法和实例工厂方法,但是可以使用到FactoryBean;

  2),对于没有源代码的类(框架内部的预定义类),只能用XML配置;


* bean组件版型标签


	@Service用于标注业务层组件
	@Controller用于标注控制层组件（如struts中的action）
	@Repository用于标注数据访问组件，即DAO组件
	@Component泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注


* 指定bean的作用域：@Scope("prototype")/("singleton")
相当于


	<bean id"xxxBean" class="com.sieg.oa.domain.xxxBean" scope="prototype"/>

* 初始化和销毁方法


	//相当于<bean init-method="init" />
	@PostConstruct
	public void init() {}
	//相当于<bean destroy-method="destory" />
	@PreDestroy
	public void destory() {


* 思考 : 选用xml还是注解：

1),Annotation:使用方便,XML文件很小,但是,依赖关系又重新回到了代码当中;

2),XML:使用稍微复杂,但是,代码很干净,代码不会很任何框架产生关系;XML安全;

### 繁琐事务设计模式

* 装饰设计模式

```
开启事务
事务
销毁事务
---(异常)
事务回滚
```

```
//员工事务类
	class EmployeeServiceImpl implements IEmployeeSerive {
		void save(){...}
		void update(){...}
	}
```

```
//事务管理类
	class ServiceManager{
		void init(){...}
		void destroy(){...}
		void rollback(){...}
	}
```

```
//员工事务包装类
	class EmployeeServiceWrapper {
		private IEmployeeSerive employeeService;
		private ServiceManager serviceManager;
		//通过构造器将内部的真是类型暴露出去
		public EmployeeServiceManager(IEmployeeSerive employeeService,ServiceManager serviceManager){
			this.employeeService=employeeService;
			this.serviceManager=serviceManager;
		}

		void save(){
			try{
				serviceManager.init();
				employeeService.save();
				serviceManager.destory();
				}catch(Exception e){
					e.printStackTrace();
					serviceManager.rollback();
				}
		}
		void update(){
			try{
				serviceManager.init();
				employeeService.update();
				serviceManager.destory();
				}catch(Exception e){
					e.printStackTrace();
					serviceManager.rollback();
				}
		}
	}
```

```
//测试类
@Test
void testEmplyeeServiceWrapper{
	EmployeeServiceWrapper employeeServiceWrapper = new EmployeeServiceWrapper();
	employeeServiceWrapper.save();
	employeeServiceWrapper.update();
}
```
* 装饰设计模式存在的问题

>1.会暴露真实对象--不安全

>2.需要为每一个增强的类定义一个包装类

### Spring的代理
* 静态代理

```
	class EmployeeServiceProxy {
		@Setter
		private IEmployeeSerive employeeService;
		@Setter
		private ServiceManager serviceManager;

		void save(){
			try{
				serviceManager.init();
				employeeService.save();
				serviceManager.destory();
				}catch(Exception e){
					e.printStackTrace();
					serviceManager.rollback();
				}
		}
		void update(){
			try{
				serviceManager.init();
				employeeService.update();
				serviceManager.destory();
				}catch(Exception e){
					e.printStackTrace();
					serviceManager.rollback();
				}
		}

	}
```

applicatioContext.xml
```
<!--开启DI注解解析程序-->
<Context:annotation-config>

<!--静态代理-->
<bean id="" class="...EmployeeServiceProxy">

	<property name="employeeService">
		<bean class="..."/>
	</property>

	<property name="serviceManager">
	<!--该bean为私有属性 不会暴露到外面 -->
		<bean class="..."/>
	</property>

</bean>
```
* 静态代理的优缺点

优点
>业务类只要关注业务逻辑本事,保证了业务类的重用性

缺点
>代理对象的某个接口只服务于某个类型的对象,也就是说每个真实对象都得创建一个代理对象
>如果需要代理的方法很多,则需要为每一种方法都进行代理处理
>如果接口增加一个方法,除了所有实现类苏姚实现这个方法,所有代理类也需要实现次方法,增加代码维护的复杂度

* 动态代理

	- jdk动态代理(只能对接口经行代理)

	是在程序运行期间由JVM通过反射机制动态生成,所以不存在代理类的字节码文件,代理对象和真是对象的关系是在程序运行期间才建立的.


```
public class TransactionHandler implements InvocationHandler{
	@Setter
	private Object target;//需要代理的真实对象(在运行时配置)
	@Setter
	private TransactionManager txManager;

	//创建代理对象
	public <T>T getProxy(){
		return <T>T Proxy.newProxyInstance(getClass().getClassLoader(),//类加载器
																			 target.getInterfaces(),//代理的对象具体有哪些规范
																			 this);
	}

	//如何对方法做增强（当前被调用方法的实参）
	public Object invoke(Object proxy,Method method, Object[] args) throws Throwable{
		try{
			txManager.begin();
			//原封不动调用真是对象的方法
			Object result = mthod.invoke(target,args);
			txManager.commit();
			return result;
			}catch(Exception e){
				txManager.rollback();
				e.printStackTrace();
				throw new RuntimaException(e);
			}
	}
}
```

Test
```
		public class Test{
			@Autowire
			private TransactionManager handler;

			@Test
			public void testSave() throws Exception{
				IEmployeeService proxy = handler.getProxy();
				proxy.save();
			}

			@Test
			public void testUpdate() throws Exception{
				IEmployeeService proxy = handler.getProxy();
				proxy.update();//该方法底层调用的是handler的invoke方法
			}
		}
```

JDK动态代理存在的问题
>1.代理的对象必须要实现接口(只能对接口做代理,也是JDK动态代理自身的约束)

>2.需要为每一个对象创建代理对象

>3.动态代理的最小单位是类(所有类的方法都会被代理)

切记不要使用@toString,递归造成栈溢出

- cglib动态代理

	原理是对指定的目标类生成一个子类,并覆盖其中方法实现增强,但因为采用的是继承,所以不能对final修饰的类进行代理.
```
	public class TransactionHandler implements InvokationHandler{
		@Setter
		private TransactionManager txManager;
		@Setter
		private Object target;//真是对象
		//获取创建代理对象
		public <T> T getProxy(){
			//cglib需要对真实类实现继承,然后重写父类的方法,从而达到增强的功能
			Enhancer hEnhancer = new Enhancer();
			hEnhancer.setSuperclass(target.getClass());
			hEnhancer.setCallback(this);
			return (T) hEnhancer.create();//创建代理对象
		}
	}
```

* 总结

>1.CGLIB可以生成目标类的子类，并重写父类非final修饰符的方法。

>2.要求类不能是final的，要拦截的方法要是非final、非static、非private的。

>3.动态代理的最小单位是类(所有类中的方法都会被处理);

>底层就是覆写父类的方法而已


>在Spring中
>若目标对象实现了若干接口，Spring就会使用JDK动态代理。
>若目标对象没有实现任何接口，Spring就使用CGLIB库生成目标对象的子类。
对接口创建代理优于对类创建代理，因为会产生更加松耦合的系统，也更符合面向接口编程规。
>cglib和javassist代理的机制都是一样的,都是通过继承实现的.



### Spring的AOP

* AOP介绍

	(Aspect Oriented Programming)面向切面编程.AOP能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性.

* 配置

```
	<!--开启DI注解解析-->
	<context:annotation-config/>

	<!--事务管理对象-->
	<bean id ="txManager" class="com.sieg.common.TransactionManager"/>

	<!--AOP配置-->
	<aop:config>
		<!-- what:做事务增强-->
		<aop:aspect ref="txManger">
			<!--where 在那个方法上做增强-->
			<aop:pointcut id="pc" expression="excution(* com.sieg.common.service.*ServiceManager.*(..))"/>
			<!--when 在什么时机-->
			<!--真实方法正常调用之前执行-->
			<aop:before method="begin" pointcut-ref="pc"/>
			<!--真实方法正常结束之后执行-->
			<aop:after-returning method="begin" pointcut-ref="pc"/>
			<!--真是方法执行异常之后,执行-->
			<aop:after-throwing method="begin" pointcut-ref="pc"/>
		</aop:aspect>
	</aop:config>
```

* 增强类型

环绕增强
```
class TransactionManager{
	public Object allInOne(ProceedingJoinPoint point){
		Object ret = null;
		try{
			System.out.println("开启事务");
			ret = point.proceed();
			System.out.println("提交事务");
		}catch(Throwable e){
			System.out.println("回滚事务"+e.getMessage());
		}finally{
			System.out.println("释放资源");
		}
		return ret;
	}
}
```

```
<!--AOP配置-->
<aop:config>
	<!-- what:做事务增强-->
	<aop:aspect ref="txManger">
		<!--where 在那个方法上做增强-->
		<aop:pointcut id="pc" expression="excution(* com.sieg.common.service.*ServiceManager.*(..))"/>
		<!--when 在什么时机-->
		<aop:around method="allInOne" pointcut-ref="pc">
	</aop:aspect>
</aop:config>
```

* AOP注解实现方式

```
	<!--开启:IoC注解扫描解析器-->
	<context:component-scan base-package="com.sieg.common">
	<!--开启:DI注解解析器-->
	<context:annotaion-config>
	<!--开启:AOP动态代理注解解析器-->
	<aop:aspectj-autoproxy>
```

```
class TransactionManager{
	@around("pc()")
	public Object allInOne(ProceedingJoinPoint point){
		Object ret = null;
		try{
			System.out.println("开启事务");
			ret = point.proceed();
			System.out.println("提交事务");
		}catch(Throwable e){
			System.out.println("回滚事务"+e.getMessage());
		}finally{
			System.out.println("释放资源");
		}
		return ret;
	}
}
```

#### 以后都是使用xml进行配置,因为维护他人编写的类,xml最方便
