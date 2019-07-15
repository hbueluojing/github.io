---
layout: post
title: jdbc vs r2dbc
description: 了解一些jdbc和r2dbc的常识
category: blog
---

## 什么是jdbc?

   Java Database Connectivity,它是一个连接和执行数据库操作的Java API.
   
   Java API使用JDBC驱动连接数据库。
   
   ![jdbc](/hbueluojing.github.io/images/githubpages/jdbc.png "jdbc")
   
     
## JDBC的四种驱动：

 ![jdbc-driver](/hbueluojing.github.io/images/githubpages/jdbc-driver.PNG "driver")

### JDBC-ODBC Bridge Driver

  使用ODBC driver连接数据库。
    
  converts JDBC method calls into the ODBC function calls.

---
  
  This is now discouraged because of thin driver.     
  In Java 8, the JDBC-ODBC Bridge has been __removed__ .      
  
* Advantages:

    1. easy to use.  
    2. can be easily connected to any database.
    
* Disadvantages:

    1. Performance degraded because JDBC method call is converted into the ODBC function calls.   
    2. The ODBC driver needs to be installed on the client machine.
   
   
### Native-API driver
    
  将JDBC调用转换为对数据库客户端API的调用。
     
  The driver converts JDBC method calls into native calls of the database API.

 ---
    
* Advantage:

    performance upgraded than JDBC-ODBC bridge driver.

* Disadvantage:
    
    1. The Native driver needs to be installed on the each client machine.  
    2. The Vendor client library needs to be installed on client machine.   
    需要在客户端加载数据库厂商 提供的代码库


### Network Protocol Driver

  The Network Protocol driver uses middleware (application server) that converts JDBC calls directly or indirectly into the vendor-specific database protocol.
   
  JDBC先把对数局库的访问请求传递给网络上的中间件服务器. 中间件服务器再把请求翻译为符合数据库规范的调用,再把这种调用传给数据库服务器. 

---
   
* Advantage:

    No client side library is required because of application server that can perform many tasks like auditing, load balancing, logging etc.

* Disadvantages:
    
    1. Network support is required on client machine.  
    2. Requires database-specific coding to be done in the middle tier.   
    3. Maintenance of Network Protocol driver becomes costly because it requires database-specific coding to be done in the middle tier.   


### Thin Driver

  The thin driver converts JDBC calls directly into the vendor-specific database protocol.   
    
  把JDBC调用转换为符合相关数据库系统规范的请求。

---
    
* Advantage:

    1. Better performance than all other drivers.   
    2. No software is required at client side or server side.
 
* Disadvantage:  
  Drivers depend on the Database.
    
    
## Java Database Connectivity with 5 Steps

1. Register the Driver class  
2. Create connection  
3. Create statement  
4. Execute queries  
5. Close connection  


```
import java.sql.*;  
class OracleCon{  
public static void main(String args[]){  
try{  
//step1 load the driver class  
Class.forName("oracle.jdbc.driver.OracleDriver");  
  
//step2 create  the connection object  
Connection con=DriverManager.getConnection(  
"jdbc:oracle:thin:@localhost:1521:xe","system","oracle");  
  
//step3 create the statement object  
Statement stmt=con.createStatement();  
  
//step4 execute query  
ResultSet rs=stmt.executeQuery("select * from emp");  
while(rs.next())  
System.out.println(rs.getInt(1)+"  "+rs.getString(2)+"  "+rs.getString(3));  
  
//step5 close the connection object  
con.close();  
  
}catch(Exception e){ System.out.println(e);}  
  
}  
}  
```
---


## JDBC connection pools.

  Making JDBC connections and closing them for every operation was expensive and time consuming  
    
  Thus came the concept of JDBC connection pools.

---
  
  
## R2DBC (Reactive Relational Database Connectivity)
    
  Java uses JDBC as the primary technology to integrate with relational databases.   
  
  __JDBC is of a blocking nature__ – there’s nothing sensible one could do to mitigate the blocking nature of JDBC.    
  
  JDBC的调用通常推积在一个队列当中, 一旦线程请求达到饱和, 线程池就会再次被阻塞.
    
  The first idea for how to make calls non-blocking is __offloading JDBC__ calls to an Executor (typically Thread pool). 
  
  为实现关系型数据库的reactive programming,引入了r2dbc。
  
  截至目前，R2DBC有三种驱动实现:
  
  * PostgreSQL
  * H2
  * Microsoft SQL Server
  
  R2DBC 附带API规范（r2dbc-spi）和客户端（r2dbc-client），使SPI可用于应用程序。  
  
  
  ```
  R2dbc r2dbc = new R2dbc(connectionFactory);
     
     Flux<Integer> count = r2dbc.inTransaction(handle ->
       handle.createQuery(
         "INSERT INTO legoset (id, name, manual) " +
         "VALUES($1, $2, $3)")
         .bind("$1", 42055)
         .bind("$2", "Description")
         .bindNull("$3", Integer.class)
         .mapResult(io.r2dbc.spi.Result::getRowsUpdated));
     
     Flux<Map<String, Object>> rows = r2dbc
       .inTransaction(handle -> handle.select(
         "SELECT id, name, manual FROM legoset")
       .mapRow((row, rowMetadata) -> collectToMap(row, rowMetadata));
  ```

---   
  
## SPRING-DATA-R2DBC
 
  Spring Data R2DBC offers the popular Repository abstraction based on R2DBC.
  提供基于r2dbc的流行存储库抽象。
  
  Spring Data R2DBC aims at being conceptually easy. 旨在简化概念。 
   
  In order to achieve this it does __NOT__ offer caching, lazy loading, write behind or many other features of ORM frameworks.
  不提供缓存、懒加载、后写或其他ORM框架的特性。
  
  This makes Spring Data R2DBC a simple(简单), limited(有限), opinionated(自以为是) object mapper(对象映射器).
  
  
  
### Object Relational Mapping

>  ORM stands for Object-Relational Mapping (ORM) is a programming technique for converting data between relational databases and object oriented programming languages such as Java, C#, etc.     

  用于关系型数据库和面向对象语言之间的数据转换。如Hibernate就被广泛使用。
  
  An ORM system has the following advantages over plain JDBC .
  
  |Sr.No.| Advantages                                     |
  | -----| :-------------------------:                    |
  |   1	 | 让业务代买访问对象而不是数据库的表。                 |                  
  |   2	 | 隐藏来自OO逻辑的SQL查询的详细信息。                 |
  |   3	 | Based on JDBC 'under the hood'. 基于jdbc引擎盖。 |
  |   4	 | 无需处理数据库实现.                               |                        
  |   5	 | Entities 基于业务概念而不是数据库结构。             |
  |   6	 | 事务管理和自动主键生成。                           |
  |   7	 | 应用程序快速开发。                                |


## rxjava-jdbc

   尽管它采取的是普通堵塞式的JDBC驱动程序，但是可以让我们的应用程序以不会堵塞的方式与数据库进行交互。  
   
   它是通过在不同线程上调度背后的阻塞来实现的 此外，它还具有DSL，可以将SQL语句和结果建模为流Stream，这些整合都使得响应式编程变得更简单。    
   
   Rxjava2-jdbc还提供了非阻塞连接池。通常在阻塞应用程序中，如果线程被阻塞，会一直到有可用的连接才会继续。   
   
   但是这对于非阻塞应用程序来说却是一个问题，因为非堵塞应用会启动新的数据库连接，这将很快耗尽所有数据库连接线程，并将美妙的非阻塞应用程序变为笨重的阻塞应用程序。   
   
   在rxjava2-jdbc中，其连接池将返回由连接池控制的一个Single，这意味着，不是为每次查询分配一个线程，查询会订阅连接池，并在有可用时接收连接 - 同时不会阻塞应用程序的线程！