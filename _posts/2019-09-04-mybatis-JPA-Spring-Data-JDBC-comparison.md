---
layout: post
title: Mybatis vs JPA vs Spring Data JDBC
description: 比较三种常见的ORM 框架
category: blog
---

## 概要

### Mybatis

   MyBatis is a first class persistence framework with support for __custom SQL__, stored procedures and advanced mappings. 
   
   MyBatis __eliminates__ almost all of the __JDBC code__ and manual setting of parameters and retrieval of results. 
   
   MyBatis can use __simple XML or Annotations__ for configuration and map primitives, Map interfaces and Java POJOs 
   (Plain Old Java Objects) to __database records__.
   
### JPA

  The Java Persistence API (JPA) is a __specification__ for __object-relational mapping__ in Java. 
  
  As for most standards within the Java Community Process, it is implemented by different frameworks.
   
  The most popular one is __Hibernate__.
  
  The JPA contains such parts:
  
  - API: the packages under javax.persistence
  
  - Java Persistence Query Language (JPQL)
  
  - object/relational metadata
  
### Spring Data JDBC

  The idea behind Spring Data JDBC is to provide access to relational databases without submitting to the complexities of JPA.

  JPA offers such features as lazy loading, caching, and dirty tracking.
 
  Spring Data JDBC aims at a much simpler model. There won’t be caching, dirty tracking, or lazy loading. 

  - SQL statements are issued when and only when you invoke a repository method. 

  -  The object returned as result of that method is fully loaded before the method returns.

  -  There is no "session" and no proxies for entities.

  -  All this should make Spring Data JDBC easier to reason about.
  
  
  | ORM<br>framework | entity<br>association | develop<br>code | debug<br>performance tuning | manual process control | cascade      | lazy<br>loading | community<br>support |
  | ------------- | :----------------: | :---------:  | :----------------------:     | :--------------------: | :----------: | :---------:  | :-------------:   |
  | Mybatis | The __association__ element deals with a "has-one" type relationship.<br>The __collection__ element deals with a "has-many" type relationship.|Need to maintain additional xml files on sql statements| Easy to track and debug, easy to locate problems and solve performance problems.| MyBatis is a first class persistence framework with support for custom SQL | Support cascading query & delete & updates | lazyLoadingEnabled and aggressiveLazyLoading support lazy load | Github data:<br>2979 commits<br>29 releases<br>131 contributors |
  | JPA | JPA can manage the association automatically, you just need to config the relationship between entities | The JPA provides some useful methods to do CRUD operation, even you only need to declare the method in a interface by some rules and the framework can auto generate the implementation | The framework does a lot of automatic operation which is not controlled by us, so the debug and performance tuning needs a lot of experience. | We can almost control the process as most of the work is done by the framework, what we can do is the interface it supports. |   Cascade is easy use by setting different | Lazy load is easy to use and disabled | Github data<br>9849 commits<br>184 releases <br>362 contributors |
  | Spring Data Jdbc | has one-to-one, one-to-many, but not support many-to-one, and it requires convention. |it is easy once you have the Repo method, but not sure how complex when the project goes huge.| it is easy to do the debug since you can know every step execution |  | support cascade query, delete, create, but can not disable, and the one-one reverse side can not do the cascade | not support | it is a new project released first version last year, some question is hard to find answer |
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    