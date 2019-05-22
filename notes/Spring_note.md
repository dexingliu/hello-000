Spring

Day01，概述，SpringIOC入门（xml）Spring的Bean管理、属性注入

Spring:是一个开源，EE开发的一站式框架，有EE开发每一层的解决方案

Web层：SpringMVC

Service层：Spring的Bean管理。Spring声明式事务

Dao层：Spring的jdbc模板，spring的ORM模块

Spring IOC：控制反转（将对象的创建权交给Spring，不需要new）

优点是可以不修改源代码就可以实现不同的类型实现类。。 

IOC实现原理：工厂+反射+配置文件

ApplicationContext.xml配置文件里

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps1.jpg) 

SpringDemo1.java测试类里

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps2.jpg) 

IOC和DI的区别（面试会问）

DI是依赖注入，前提必须有IOC的环境，Spring管理这个类的时候将类依赖的属性注入进来

 

Spring的工厂类

BeanFactory在调用getBean时才会生成实例

ApplicationContext：新版本的工厂类，继承了BeanFactory在new时就会生成

ClassPathXmlApplicationContext：加载类路径下的配置文件

FileSystemXmlApplicationContext：加载磁盘上的配置文件

 

Spring的配置：XML提示的配置：

Bean标签的id根name有什么区别，id使用了约束中的唯一约束（不能出现特殊字符），name没有（可以有特殊字符），在Spring和Struts1框架整合的时候必须使用name=、什么

Bean标签的生命周期：init-method  destory-method

Bean 的作用范围配置（scope属性）

Singleton（默认Spring采用单例模式创建对象，相当于只new一次）

Protype（多例模式，用一次new一次）

Request：应用在web项目中，Spring创建这个类后，存入request域中

Session：保存到session中

Globalsession：应用在web项目中，必须porlet（子系统不需要登录）环境下使用

Spring的属性注入（DI）：方法有三种，Spring当中的属性注入支持前两种

构造方法的方式bean里写id和class

Set方法直接是get set，在bean的property里写name和value（属性）/ref（对象）

接口注入的方式

P名称空间完成属性的注入：普通属性P:属性名=””  对象属性P:属性名-ref=””

SpEL的属性注入（3.0）：#{语法}，，可以进行计算或者调用其他方法。

集合属性的注入：在property下面使用子标签<List>下面再选择value或者ref即可 

 

Spring的分模块开发的配置：可以在加载配置文件时加载多个，也可以在一个配置文件中引入别的（<import resource=”applicationContext2.xml”/>）

 

 

 

Day02

Spring IOC的注解开发

注解入门，Spring4以后除了6个包还要引入AOP的包，创建applicationContext.xml，引入约束context（包含了beans），需要开启Spring的组件配置扫描

<context:component-scan base-package=”com.itheima.spring.demo1”>

然后在类上加注解，@component(value=”userDao”)，相当于配置了<bean,id=””,class=””>

最后再使用getbean。

如果属性没有set方法，需要将属性注入的注解添加到属性。有set就加在set方法上。

详细注解：

@Component组件：用来修饰一个类，前提是配置好组件扫描，有三个衍生注解

@controller：web层

@Service：Service层

@repository：dao层

属性注入：

普通属性@Value

对象类型属性：@Autowired，按照类型进行注入，要想按照名称注入，需要加@qualifier

@Resource:完成对象类型注入，并且按照名称注入。

其他注解：

初始化：postConstruct

销毁：PreDestroy

Bean的作用范围：@Scope（singleton Prototype request session）尤其在ST和SP整合

Spring的xml和注解整合开发：XML适合所有场景，注解有时用不了（类不是Spring提供的）

XML配置bean，注解完成属性注入。（可以不用开类上的组件扫描，但是要开属性扫描，<context:annotation-config/>）

 

Spring的AOP的XML开发 

AOP：面向切面编程，是OOP的扩展和延伸，采用横向抽取机制(代理)取代传统的纵向继承。

生成所有dao的代理对象，访问代理对象，

Spring底层AOP实现就是采用动态代理。JDK（只能对实现了接口的类代理）、Cglib动态代理（对没有接口的类产生代理对象，不用final修饰，生成子类对象）底层自动切换

AOP可以进行权限校验，日志记录，性能监控，事务控制

JDK动态代理：

Cglib动态代理：第三方，需要引包

用配置方法产生代理：使用AspectJ的XML方式。。Project:Spring4_day02_aop

Spring 整合junit单元测试，需要导包：spring-test

Spring 中的通知类型:主要是为了得到切入点信息，增强方式

前置通知<aop:before method=”” pointcut-ref=””/>

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps3.jpg) 

后置通知：<aop:after-returning/>还可以获得方法的返回值（比如日志记录）

环绕通知：阻止目标方法的执行（进行性能监控），有返回值

异常抛出通知：<aop:after-throwing/>

最终通知：after

切入点表达式：

基于execution函数完成的

[访问修饰符] 方法返回值 包名.类名.方法名(参数)

 

 

Day03

Spring的AOP开发其实就是在配置文件中开启自动代理

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps4.jpg) 

然后在切面类上直接加注解

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps5.jpg) 

但是后期维护要改代码修改代码比较麻烦，所以可以将切入点的配置放在一起

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps6.jpg) 

类没有使用接口会用cjlib代理，实现了接口就用jdk动态代理

Spring AOP的注解 通知类型：

前置@before后置@After Returning环绕@Around异常抛出After Throwing

Spring提供了很多持久层技术的模板

​    ![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps7.jpg)

JDBC模板入门：

导入Jar包，mysql数据库驱动，Spring里面JDBC模板（jdbc/tx/test/aop），建数据表

将创建连接池和创建jdbc模板的事都交给Spring，引入Spring的配置文件。

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps8.jpg) 

使用开源数据库连接池配置

DBCP：dbcp和pool包，然后配置

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps9.jpg) 

C3P0配置：c3p0包，然后配置

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps10.jpg) 

实际开发中不把参数写到applicationContext.xml中，单独提取成一个property文件，然后在Spring配置文件中引入：有两种方式，一是通过bean标签，二是通过context标签

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps11.jpg) 

然后在连接池配置中的value =${jdbc.username}

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps12.jpg) 

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps13.jpg) 

事务管理

API：1、platform transaction manager平台事务管理器

DataSourcetransaction manager:底层使用JDBC

Hibernatetransaction manager：底层使用hibernate

2、Transaction Definition事务定义信息：用于事务隔离级别，超时，传播行为，是否只读

3、transaction Status事务的状态

首先，平台事务管理器根据事务定义信息进行事务的管理，在事务管理过程中产生各种事务状态，记录下来。

事务的传播行为：主要是解决业务层方法之间的相互调用问题，有七种

保证多个操作在同一个事务中：PROPAGATION_REQUIRED(如果A有事务就用，没有就新)

保证多个操作不在同一事务中：PROPAGATION_REQUIRES_NEW(如果A有事务就挂起，创建一个新的只包含本操作的事务，如果没有事务，也要新建一个本操作的事务)

嵌套式事务：PROPAGATION——NESTED(如果A有事务就执行，完了设置一个保存点，然后执行B的操作，有异常就回滚，可回滚到存档前后)

Spring的事务管理：一类：编程时事务管理

第一步，配置平台事务管理器，

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps14.jpg) 

第二步，配置事务管理模板

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps15.jpg) 

第三步，注入事务管理模板

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps16.jpg) 

第四步，编写事务管理的代码

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps17.jpg) 

Spring的事务管理：二类：声明式事务管理（底层就是AOP的思想）

XML方式的声明式事务管理

（1）导包

（2）在tx2.xml中配置事务管理器

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps18.jpg) 

（3）配置事务的增强（切面）

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps19.jpg) 

（4）AOP的配置，将增强应用到目标类

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps20.jpg) 

（5）测试

注解式的声明式事务管理

（3）开启注解事务

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps21.jpg) 

（4）在业务层添加注解

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps22.jpg) 

Dao的实现类直接继承JdbcDaoSupport就可以有jdbc模板和连接池

Day04  SSH整合

整合方式：无障碍整合、将hibernate配置交给Spring、hibernate模板的使用、延迟加载

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps23.jpg) 

MVC框架                        一站式框架                      ORM框架

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps24.jpg)web层使用的是servlet开发就必须这样，Struts2不用

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps25.jpg)![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps26.jpg)![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps27.jpg) 

 

 

SSH整合一（带hibernate配置文件）

1、导包：各种包

2、引入配置文件

Struts：web.xml  struts.xml

Hibernate：hibernate.cfg.cml(删除线程绑定session、隔离级别)  映射文件

Spring：web.xml  applicationContext.xml  日志记录

3、创建包结构：domain/dao/service/web.action

4、创建相关类：Customer/CustomerAction/CustomerService  CustomerServiceImpl / CustomerDao  CustomerDaoImpl

5、引入相关页面：css  images  js  jsp  页面

6、修改add.jsp

7、编写Action，方式一（Action由struts2配置）struts.xml中配置Action，提交数据到Action，在Action中引入service，需要导入struts-spring-plugin插件包，在applicationContext.xml中配置service把它交给spring管理，在Action中注入customerService（提供Service和set方法），然后就可以直接在save中调用customerService的save

8、Spring整合Struts2的方式二（Action由Spring配置）struts-spring-plugin插件包 在spring配置Action后，Struts.xml中的class直接写Spring中的id。

**注意**：需要在spring中配置Action为多例scope=”prototype”

需要手动注入Service，ref=””

9、业务层Service调用dao:Spring管理dao，在service中注入dao，

10、创建数据库和表，编写domain实体和创建映射Customer.hbm.xml

11、Spring和hibernate整合：在Spring配置文件中，引入hibernate的配置信息(在dao中直接注入sessionfactory创建session模板)，在dao中使用session模板完成save

12、配置Spring的事务管理，先配置事务管理器，开启注解事务，在业务层使用注解

SSH整合二（不带hibernate配置文件）

数据库连接的配置，hibernate相关的属性配置，c3p0连接池，映射文件

Spring延迟加载方案：

哪些地方会出现延迟加载：使用load方法查询对象、查询到某个对象以后显示其关联对象

Session在业务层开启和关闭，但是真正使用这个对象是在web层，所以这时候回报No-session异常。在实际开发中，hibernate没有提供解决方案，Spring提供了一个过滤器：

在web.xml中配置过滤器OpenSessionInViewFilter：在视图层获得session和关闭session，这样查询到的对象才是持久态，而且过滤器位置要在struts之前

 