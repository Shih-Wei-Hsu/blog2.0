---
layout: post
title:  "Hibernate SessionFactory的创建方式"
date:   2017-07-09 22:05:00 +0800
tags:  xsw java
category:  2017年7月
keywords: hibernate,sessionFactory
description: "Hibernate SessionFactory的创建方式,包括hibernate3、4、5版本"
type: coding
---
### 一. hibernate 3 创建 SessionFactory 的方式
```java    
Configuration configuration = new Configuration().configure();

SessionFactory sessionFactory = configuration.buildSessionFactory();
```
### 二. Hibernate 4 创建 SessionFactory 的方式

#### 方式一:
```java
Configuration configuration = new  Configuration().configure();

ServiceRegistry serviceRegistry = new ServiceRegistryBuilder().applySettings(configuration.getProperties()).build();   
```
在这里使用build方法或者buildServiceRegistry方法都可.
```java
sessionFactory = configuration.buildSessionFactory(serviceRegistry);
```
> 注:学习使用中,发现在Hibernate 4.3.11 下 org.hibernate.service.ServiceRegistryBuilder居然过时,故找到第二种方式如下:

#### 方式二:
```java
Configuration configuration = new  Configuration().configure();

ServiceRegistry serviceRegistry = new StandardServiceRegistryBuilder().applySettings(configuration.getProperties()).build();

sessionFactory = configuration.buildSessionFactory(serviceRegistry);
```
至此,可见 hibernate 3 和 hibernate 4 SessionFactory 的创建方式 异同之处:
> *  1.SessionFactory 的创建都需要由Configuration对象的buildSessionFactory方法进行创建.  (同)
> *  2.在hibernate 3 中 ,buildSessionFactory()方法不带参;而hibernate 4 中,buildSessionFactory(serviceRegistry) 带参 参数是 ServiceRegistry对象. (异)

> 注:在hibernate3中,由Configuration直接创建SessionFactory对象.而4中,需要传入ServiceRegistry对象,才可创建.
   ServiceRegistry的意义: ServiceRegistry是API中的一个接口,是Hibernate中Service的注册表,为Service提供了一  
   个统一的加载,初始化,存放以及获取的机制.
   在hibernate4中,两种方式的区别在: ServiceRegistryBuilder 过时,由StandardServiceRegistryBuilder 代替.

> 再来看看Hibernate5中的创建方式:

### 三. Hibernate 5 创建 SessionFactory 的方式
```java
   StandardServiceRegistry standardRegistry = new StandardServiceRegistryBuilder()
                                             .configure().build();
   Metadata metadata = new MetadataSources(standardRegistry).getMetadataBuilder()
       .applyImplicitNamingStrategy(ImplicitNamingStrategyJpaCompliantImpl.INSTANCE)                                                       
                             .build();
   SessionFactory sessionFactory = metadata.getSessionFactoryBuilder().build();
```
   Hibernate5不再显式的使用Configuration类代表配置信息，而是新增了一个Metadata类来处理，
   SessionFactory的创建从Metadata实例中获得一个创建者，
   然后调用改创建者的build()方法获得SessionFactory。这是典型的创建者模式。
  


