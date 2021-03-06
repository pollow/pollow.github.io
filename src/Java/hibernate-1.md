title: Hibernate 入门笔记
Date: 2016-02-05

Java 项目库看多了，尤其是Apache的，换上了严重的文档阅读恐惧症，严重倾向于阅读经过加工后的中文教材，然后去看reference。Getting Start真没什么好东西。

## 基础

Hibernate 是一个 ORM 框架，全称 Object-Relative Database-Mapping，显而易见，是一个在Java对象和关系型数据库之间建立连接的**中间件**。

Hibernate在2005年被JBoss收购，JBoss之后被Red Hat收购，所以现在算起来HIbernate是Red Hat旗下的开源库了。

早起Hibernate使用XML配置POJO（我现在的工程中使用的方法），现在的Hibernate支持JPA（Java Persistence API），也可以使用注解的方法配置（今天在浏览 Spring 框架的时候，看到也有一个 Spring Data，之后也可稍作研究）。

## 使用笔记

创建一个类，推荐为POJO，使用`@Entity`注解，使之为一个实体类，既与数据库有映射关系。实体类还需要在类声明处用`@Table`注解表明，属性处用`@id`定义主键，`@Column`定义列；还有其他的一些外键和类型注解。

Hibernate 通常还需要一个工具类，官方提供了HibernateUtil，用于方便创建Session，Transaction等。`SessionFactory`是一个线程安全的 Session 工厂类；得到Session后，每次执行请求，都需要打开Session，打开Transaction，执行请求，关闭Transaction，关闭Session。这种重复工作显然不合理。

在Web中使用 Hibernate，配置文件要设置`current_session_context_class`为thread (for tomcat,不知为何)。

推荐为 Hibernate 设置乐观锁，在POJO中增加一列

```java
@Version
private int version;
```

问题是，会在数据库表中增加一列Version么？难道数据库的全部配置都要交给Hibernate，由Hibernate自动创建数据库么？一般来说，不都是DBA完成数据库的创建、调优，然后中间层只是方便CURD么？那么Hibernate如何利用View呢？

## Entity Class

当一个类被`@Entity`注解标记的时候，说明这个类：

1. 使用了标准的 JavaBean 约定。
2. 必须包含无参构造函数，Hibernate会使用无参构造函数，利用Java反射创造实体。
3. Entity的属性默认为数据库的列，即使没有任何注解标明。

## 外联关系

Hibernate会自动处理复杂的外链关系，在声明POJO的时候需要配合注解使用。在配置文件中可以设置，取数据时是否连带外键一同获取，并且设置深度。cascade属性可以配置是否保存对象的同时保存外键。

查询外键的时候有延时加载和即时加载两种模式，推荐使用延迟加载（默认选项），获取更好的性能，但是也要考虑可能的异常（数据库连接断掉）。

## HQL

HQL相对于SQL，只是针对面向对象的设计做了小修改。需要有如下注意点：

1. 在设计类名、包名时，大小写敏感
2. 可以使用`select class`的方法获取对象，这种情况下也可以省略select语句。
3. 如果查询的不是整个对象，而是一些字段，可以选择返回`Object[]`, `List`, `Map`，语法细节查询文档。
4. where字句允许通过.(dot)进行简单的外键访问，允许对集合类型调用size函数。
5. 推荐使用名称占位符`:name`写入数据，调用`setParamter`系列函数传入数据（不知道是否会转义）。
6. 分页可以调用Query对象的`setFirstResult`和`setMaxResults`函数。
7. 也支持各种join查询。
8. 用`SQLQuery`可以使用SQL进行查询，通过设置`addEntity`可以配置返回实体。
9. 常用的简单查询可以使用`@NamedQuery`配置。	

## JPA

JPA 是 Java 指定的一组关于持久化的接口，Hibernate提供了相关的服务。相比于标准的 Hibernate 配置，JPA有自己的启动配置文件，名为`persistence.xml `。和`hibernate.cfg.xml`有所不同，但也十分接近。

具体的配置可以参考[这个链接](http://docs.jboss.org/hibernate/orm/5.1/userguide/html_single/Hibernate_User_Guide.html#bootstrap-jpa)。

虽然相对于 Native Hibernate API 损失了一些灵活性，并且无法利用更加OO的HQL，但是还是更加推荐使用JPA。官方统一的标准意味着后期更换 JPA Provider 会比较容易（虽然我认为没有必要）。

## Spring DAO模块

如上所述，进行查询的时候通常需要一系列打开，关闭的琐碎操作，DAO层封装了这些操作。

## Spring Service模块

## Spring Data

## jOOQ

## torpedoquery

## Questions

`@GeneratedValue`是否会和数据库默认的auto increment 冲突？

