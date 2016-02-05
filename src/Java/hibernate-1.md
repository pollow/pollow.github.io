title: Hibernate 入门笔记
Date: 2016-02-05

Java 项目库看多了，尤其是Apache的，换上了严重的文档阅读恐惧症，严重倾向于阅读经过加工后的中文教材，然后去看reference。Getting Start真没什么好东西。

## 基础

Hibernate 是一个 ORM 框架，全称 Object-Relative Database-Mapping，显而易见，是一个在Java对象和关系型数据库之间建立连接的**中间件**。

Hibernate在2005年被JBoss收购，JBoss之后被Red Hat收购，所以现在算起来HIbernate是Red Hat旗下的开源库了。

早起Hibernate使用XML配置POJO（我现在的工程中使用的方法），现在的Hibernate支持JPA（Java Persistence API），也可以使用注解的方法配置（今天在浏览 Spring 框架的时候，看到也有一个 Spring Data，之后也可稍作研究）。

## 使用笔记

创建一个类，推荐为POJO，使用`@Entity`注解，使之为一个实体类，既与数据库有映射关系。实体类还需要在类声明处用`@Table`注解表明，属性处用`@id`定义主键，`@Column`定义列；还有其他的一些外键和类型注解。

Hibernate 通常还需要一个工具类，官方提供了HibernateUtil，用于方便创建Session，Transaction等。`SessionFactory`是一个线程安全的 Session 工厂类，

在Web中使用 Hibernate，配置文件要设置`current_session_context_class`为thread (for tomcat,不知为何)。

推荐为 Hibernate 设置乐观锁，在POJO中增加一列

```java
@Version
private int version;
```

问题是，会在数据库表中增加一列Version么？难道数据库的全部配置都要交给Hibernate，由Hibernate自动创建数据库么？一般来说，不都是DBA完成数据库的创建、调优，然后中间层只是方便CURD么？那么Hibernate如何利用View呢？


## Questions

`@GeneratedValue`是否会和数据库默认的auto increment 冲突？
