![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps28.jpg) 

Day01

配置文件

XML的配置（映射的配置maping）

核心配置（必须，可选，映射文件引入）

核心API

Configuration对象（加载核心、映射配置）

Session Factory对象（充当数据存储连接池并创建session，线程安全为全局，一个项目只需要一个，可抽取工具类，log4j文件放在SRC下）

Session对象（Hibernate与数据库连接的对象，线程不安全，不能为全局,面试会问使用get和load方法查询的区别：

Get发送SQL语句时立即加载,load使用对象时延迟加载。

Get 加载的是真实对象，load返回代理对象

Get查不到返回null，load查不到返回一个异常

Transaction对象（负责管理实务，begin，commit，rollback）

 

Day02

1、辨析持久化类的编写规则（5条），延迟加载不管用了这时。

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps29.jpg)/

2、主键生成策略（开发中使用代理主键，increment HBNT自动增长、identity数据库底层自动增长（Oracle通过序列增长，没有自动增长）、sequence采用序列的方式、UUID适用于字符串类型 、native本地策略可以在identity和sequence切换、assigned需要用户自己设置主键）

3、持久化的三种状态：瞬时态（没有唯一的表示OIDye没有被session管理）、持久太（有OID被管理）、托管态（有OID没有被管理）。

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps30.jpg) 

三种状态的获取与转换

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps31.jpg)![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps32.jpg) 

持久态对象在数据有变化时会自动更新数据库，不需要自己写update，没变	化就不更新。这种持久态自动更新依赖于hibernate底层的一级缓存。提交时会比较缓存区和快照区，如果不一致就会更新数据库

 

4、hibernate一级缓存：

(1) 一级缓存：是session级别的缓存，生命周期一致，是自带的不能卸载

主要有ActionQueue和persistenceContext构成

(2) 二级缓存：是session Factory，是需要配置的缓存，用Redis替代。

在一级缓存中找不到，才会去查询数据库，session.close缓存才清理。

内部结构：快照区（例如百度快照）

5、hibernate事务管理：事务是同时成功同时失败

事务的特性：原子性、一致性、隔离性（会引发安全问题：读（脏读、不可重复读、虚读）、写）、持久性

脏读（一个事务读到另一个未提交的数据）

不可重复读（读到已经提交update的数据，多次查询不一致）

虚读（读到另一个事务已经提交的insert数据，多次查询不一致）

设置事务隔离级别：

Read uncommitted：以上读问题都会发生

Read committed：解决脏读

Serializable：解决脏读和不可重复读

热捧able read：解决所有问题

Hibernate中设置事务隔离级别：通过配置文件，在核心配置文件中

Property name=“hibernate.connection.isolation”<1/2/4/8>

事务要加载到业务层：封装了一个业务逻辑操作。

线程绑定session：hibernate框架内部已经绑定好了ThreadLocal，在sessionFactory中提供一个getCurrentSession，需要自己写一个工具类开启。在核心配置中配置session绑定的本地线程。

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps33.jpg) 

6、hibernate其他的API

Query：query接口用于接收HQL，查询多个对象，是一种面向对象的查询。通过session.createQuery来获得。

Criteria QBC：条件查询，更加面向对象的语法。session.createCriteria

SQLQuery：

 

 

Day03

数据库表与表之间的关系

**一对多**，建表原则：在多的一方需要创建外键，指向1的一方的主键。

在多的一方引入一的对象，在一的一方引入多的集合。引入约束，然后分别写映射文件，配置核心，引入工具类。一对多的只保存一边是不行的，只能进行级联操作，级联有方向性

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps34.jpg) 

级联保存或更![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps35.jpg)新：一对多在一的一方set集合上配置cascade，多对一在多	的一方many-to-one上配置cascade

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps36.jpg) 

级联删除：在删除时会删除另一边的数据，没设置时默认修改联系人外键，	同时删除客户。级联删除是客户，需要配置cascade=delete

注意：一对多如果设置了双向关联，就会多一些多余的SQL语句，可以单项 

多对多，需要引入一个中间表。至少有两个属性，分别指向各自的主键。

双方引用集合![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps37.jpg)

分别创建映射xml，引入约束

多对多如果双向联系了，必须有一方放弃外键维护。

只保存一边，级联另外一半，配置cascade=save-update

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps38.jpg) 

级联删除，cascade=delete基本用不上

级联增删改：添加是add，删除是remove，改就是先删后加

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps39.jpg) 

 

 



Day04

Hibernate 查询方式

OID查询：使用对象的主键OID查询，通过session.get

load查询：session.load

对象导航检索:hibernate根据已经查到的一个对象获取关联对象的方式

Customer CC = Session.get(LinkMan.class,1l).getCustomer();

HQL检索:hibernate Query Language 是一种面向对象的查询方式，类似SQL

Session.createQuery()，但是不支持*

先初始化数据

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps40.jpg) 

简单查询：注意toString的时候不能死循环，只能单一toString

排序查询：Session.createQuery(“from Customer order by cus_id”)类里面的属性名

条件查询：Session.createQuery(“from Customer where cus_name = ?”)

  Query.setParameter(0,”李冰”)  多条件查询就是AND

投影查询：查询对象的某些属性(“select c.cust_name,c.cust_source from Customerc”)

分页查询：只需要调用两个方法就可以

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps41.jpg) 

分组统计查询：select count(*) from customer涉及到聚合函数的使用

HQL的多表查询：除了普通的交叉连接，内连接，外连接外，还有迫切内连接，在inner join后面加一个关键字fetch，通知hibernate将后一个对象的属性封装成集合拿出来装到第一个对象里，返回第一个对象，为了防止重复，可以select distinct c from c。

QBC检索:query by criteria 使用Criteria criteria = session .createcriteria(Customer.class)

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps42.jpg) 

排序查询：直接使用criteria.addOrder(Order.asc(“cust_id”));//升序是asc降是desc

分页查询：criteria.setFirstResult(0)  criteria.setMaxResult(10)  从0开始到10结束

条件查询：criteria.add(Restrictions.eq(“属性名”,”具体名”)) eq= >gt >=ge <lt <=le <>ne

统计查询：criteria.setProjection(projectios....)使用聚合函数

离线条件查询（SSH）：detachedCriteria适合做综合条件查询。

![img](file:///C:\Users\NIGHT_~1\AppData\Local\Temp\ksohtml21380\wps43.jpg) 

SQL检索

 

Hibernate抓取策略：查询关联对象的一种优化（容易被面试，缓存怎么弄，语句怎么优化），

抓取策略往往和关联级别的延迟加载一起使用

延迟加载(lazy=true)：执行到该行的时候不会发送语句查询，在真正使用该对象时查询：

类级别的延迟：通过load方法查询某个对象时，是否采用延迟(class标签上配lazy)

失效：将lazy设置为false，持久化类通过final修饰，通过hibernate.initialize

关联级别的延迟：在查询到某个对象时，查询其关联对象的时候是否采用延迟

在set或者many_to_one

抓取策略：通过一个对象抓取到关联对象，需要发送SQL语句，如何发，什么格式。可以通过set或者many_to_one上的lazy、fetch属性设置，

fetch控制SQL语句格式，select（默认），join（迫切左外连接），subselect（子查询）

Set上：

many_to_one上：fetch只有select和join（lazy就没用了）。Lazy只有proxy（取值取决于一的一方的lazy取值）和false

批量抓取：一次获取多个客户的联系人信息，一的一方在set上配置batch-size，多的一方在一那边的class上配置batch-size。默认为1