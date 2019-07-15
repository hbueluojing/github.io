---
layout: post
title: Clean Code 笔记 
description: 代码简洁之道要点记录
category: blog
---

## Meaningful name 命名
   <ul>
        <li>名副其实 </li>
        <li>有意义的区分，避免ProductInfo和ProductData</li>
        <li>使用读得出来的名称，毕竟编码也一种是社交</li>
        <li>使用可搜索的名称，尽量避免单字母、数字命名</li>
        <li>类名使用名词或名词短语</li>
        <li>方法名使用动词或动词短语</li>
        <li>每个抽象概念选一个词，一以贯之，如fetch,get,retrieve同义词，选一个即可</li>
        <li>重载构造方法时，使用描述参数的静态工厂方法 </li>
    </ul>
    
 ```
 Complex fulcrumPoint = Complex.FromRealNumber(23.0);
 ```
 is generally better than
 
 ```
 Complex fulcrumPoint = new Complex(23.0);
```

## Functions 函数
   <ul>
        <li>尽量短小</li>
        <li>if/else/while语句其中的代码块应该只有一行，该行大抵是一个函数调用语句</li>
        <li>只做一件事</li>
        <li>无参>1元>2元>3元函数</li>
        <li>1元函数常见的两种用法： 
            <br>1. 转换：operating on that argument, transforming it into something else and returning it
            <br>2. 事件通知：call as an event and use the argument to alter the state of the system
        </li>
        <li>函数需要3个及以上参数时，一些参数应该封装成类</li>
        <li>避免标识参数true/false</li>
        <li>命令和查询分离
            <br>Either your function should change the state of an object, or it should return some information about that object,but not both.
        </li>
        <li>try/catch代码块主体抽离出来 </li>
        <li>每个代码块只有一个入口，一个出口</li>
   </ul>

```
try{
    doSth();
   } catch(Exception ex){
	handleError();
   }
```

### SWITCH STATEMENTS

```
public Money calculatePay(Employee e) 
   throws InvalidEmployeeType {
       switch (e.type) {
         case COMMISSIONED:
           return calculateCommissionedPay(e);
         case HOURLY:
           return calculateHourlyPay(e);
         case SALARIED:
           return calculateSalariedPay(e);
         default:
           throw new InvalidEmployeeType(e.type);
       }
     }
```
to

```
public abstract class Employee {
     public abstract boolean isPayday();
     public abstract Money calculatePay();
     public abstract void deliverPay(Money pay);
   }
   -----------------
   public interface EmployeeFactory {
     public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
   }
   -----------------
   public class EmployeeFactoryImpl implements EmployeeFactory {
     public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
       switch (r.type) {
         case COMMISSIONED:
           return new CommissionedEmployee(r) ;
         case HOURLY:
           return new HourlyEmployee(r);
         case SALARIED:
           return new SalariedEmploye(r);
         default:
           throw new InvalidEmployeeType(r.type);
       }
     }
   }
```

## Comments 注释
   <ul>
        <li>代码阐释替代注释</li>
        <li>能用函数或变量名时别用注释</li>
        <li>注释掉的代码直接删除</li>
   </ul>

## Formatting 格式

* 原始代码修改之后很久，其代码风格和可读性会影响可维护性和扩展性。
* 名称一目了然。
* 源文件顶部给出高层次概念和算法，细节往下一次展开。
* 每个空白行是一条线索，标志新的独立概念。
* 遵循团队规则，团队风格在个人风格只上。

## Objects and Data Structures 对象和数据结构

* 德墨忒尔律，模块不应该了解它所操作对象的内部情形。
* 只有公共变量，没有函数的类，这种数据结构即为数据传送对象（DTO）。
* 对象暴露行为隐藏数据，使添加新类型的对象而不改变原有行为变得简单，同时给已有对象新增行为变得困难。
* 数据结构仅暴露数据而没有具体函数，使给已有的数据结构添加行为变得简单，而给已有的函数添加新的数据结构变得困难。

## Error Handling 错误处理

### checked exception and unchecked exception

   ![exception](/hbueluojing.github.io/images/githubpages/Exception.png "exception")

以上图片中，红色部分为unchecked exception，蓝色部分为checked exception。

Checked exception是必须在代码中进行恰当处理的Exception，编译器将检查是否为其提供了异常处理机制,否则编译不通过。

Unchecked Exception是不可预料的，一旦发生，程序终止。有一些是由于开发者代码逻辑错误造成的RuntimeException，有一些是 *java.lang.Error* 的子类。
 
 ----
 
 常见的RuntimeException包括： 
<br>NullPointerException - 空指针引用异常
<br>ClassCastException - 类型强制转换异常。
<br>IllegalArgumentException - 传递非法参数异常。
<br>NumberFormatException - 数字格式异常
<br>ArithmeticException - 算术运算异常
<br>IndexOutOfBoundsException - 下标越界异常
<br> NegativeArraySizeException - 创建一个大小为负数的数组错误异常
<br> ArrayStoreException - 向数组中存放与声明类型不兼容对象异常
<br>SecurityException - 安全异常
<br>UnsupportedOperationException - 不支持的操作异常
 
 ____
 
* 抛出异常，而不是返回错误码。

* 使用unchecked exception。

* 不要返回null,考虑抛出异常或者返回特殊对象。

* 不要传递null。

## Boundaries 边界

清晰分割边界代码，定义期望的测试，避免过多了解第三方代码中的特定信息。

```
Map sensors = new HashMap();
Sensor s = (Sensor)sensors.get(sensorId );
```

to 

```java
public class Sensors {
     private Map sensors = new HashMap();
 
     public Sensor getById(String id) {
       return (Sensor) sensors.get(id);
     }
   }
```
> The interface at the boundary (Map) is hidden.

## Unit Tests 单元测试
  
### TDD三大定律

* 在编写不能通过的单元测试前，不可编写生产代码。
* 只可编写刚好无法通过的单元测试，不能编译也算不通过。
* 只可编写刚好通过当前失败测试的生产代码
___

* 每个测试一个断言。
> One assert per test.

* **F**ast.**I**ndependent.**R**epeatable.**S**elf-Validation.**T**imely


## Classes 类

* java惯例，一个类由一系列变量开始。
<br> __public static__ 常量
<br> __private static__ 常量
<br> __private__ 实例变量
<br> __public__ 方法
<br> __private__ 方法
<br> 不推荐使用 __public variables__。
> There is seldom a good reason to have a public variable. 

* 类的名称应该描述它履行的职责
* 用25个左右的单词写出类的简短描述，不使用'if','and','or','but'
* 单一职责规定，类应该有且只有一个改变的理由。

## Systems 系统

* 将系统的构造和使用分开
--- 
   一个构造和使用 **没有分离**的示范：懒加载
```
public Service getService() {
    if (service == null){
	    service = new MyServiceImpl(...);
	}
        return service;
    }
```

优点：
    <br>在真正用到对象之前，无需操心这种架空构造，启始时间也会更短，而且还能保证永远不会返回 null 值。

缺点：
    <br>得到了 MyServiceImpl 及其构造所需一切的硬编码依赖，即便在运行时永不使用这种类型的对象。
    <br>测试将会是个问题。必须在单元测试调用该方法之前，就给 service 指派恰当的（MOCK OBJECT）。
    <br>由于构造逻辑与运行过程相混杂，我们必须测试所有的执行路径（例如，null 值测试及其代码块）。
    <br>有了这些权责，说明方法做了不止一件事，这样就略微违反了单一权责原则。
    <br>不知道 MyServiceImpl 在所有情形中是否都是正确的对象。
  
* 分解main  
    <br>将构造与使用分开的方法之一是将全部构造过程搬迁到 main 或被称之为 main 的模块中，
    <br>设计系统的其余部分时，假设所有对象都已正确构造和设置。
    <br>main 函数创建系统所需的对象，再传递给应用程序，应用程序只管使用。

* 工厂
    <br>有时应用程序也要负责确定何时创建对象。
    <br>比如，在某个订单处理系统中，应用程序必须创建 LineItem 实体，添加到 Order 对象。
    <br>在这种情况下，我们可以使用抽象工厂模式让应用自行控制何时创建 LineItems，但构造的细节却隔离于应用程序代码之外。

* 依赖注入

有一种强大的机制可以实现分离构造与使用，那就是依赖注入（Dependency Injection，DI），
控制反转（Inversion of Control，IoC）在依赖管理中的一种应用手段。
控制反转将第二责权从对象中拿出来，转移到了另一个专注于此的对象中，从而遵循了单一权责原则。
在依赖管理情景中，对象不应该负责实体化对自身的依赖。
反之，它应当将这份权责移交给其他“有权利”的机制，从而实现控制的反转。
因为初始设置是一种全局问题，这种授权机制通常要么是 main 例程，要么是有特定目的的容器。

Spring 框架提供了最有名的 Java DI 容器。
用户在 XML 配置文件中定义互相关联的对象，然后用 Java 代码请求特定的对象。

## Emergence

* 运行所有测试；
* 不可重复；
* 表达了程序员的意图；
* 尽可能减少类和方法的数量

## Concurrency 并发

并发是一种解耦策略。它们帮助我们把做什么（目的）和何时（时机）做分解开。

解耦目的与时机能明显地改进应用程序的吞吐量和结构。

* 限制数据作用域：谨记数据封装；严格限制对可能被共享的数据的访问。
  <br>解决方案之一是采用 synchronized 关键字在代码中保护一块使用共享对象的临界区（critical section）。
  
* 使用数据复本
    <br>1.复制对象并以只读的方式对待。
    <br>2.复制对象，从多个线程收集所有复本的结果，并在单个线程中合并这些结果。
    
* 线程应尽可能地独立：尝试将数据分解到可被独立线程（可能在不同处理器上）操作的独立子集。
    <br>1.让每个线程在自己的世界中存在，不与其他线程共享数据。
    <br>2.每个线程处理一个客户端请求，从不共享的源头接纳所有请求，存储为本地变量。
    
__ReentrantLock__ ，可在一个方法中获取、在另一个方法中释放的锁；

__Semaphore__，经典的“信号”的一种实现，有计数器的锁；

__CountDownLatch__，在释放所有等待的线程之前，等待指定数量时间发生的锁。这样，所有线程都平等地几乎同时启动；

__限定资源__：并发环境中有着固定尺寸或数量的资源。例如数据库连接和固定尺寸读／写缓存等；

__互斥__：每一时刻仅有一个线程能访问共享数据或共享资源；

__线程饥饿__：一个或一组线程在很长时间内或永久被禁止。
    <br>例如，总是让执行的快的线程线运行，假如执行的快的线程没完没了。则执行时间长的线程就会“挨饿”；

__死锁__：两个或多个线程互相等待对方结束。
    <br>每个线程都拥有其他线程需要的资源，得不到其他线程拥有的资源，就无法终止。

__活锁__：执行次序一致的线程，每个都想要起步，但发现其他线程已经“在路上”。
    <br>由于竞步的原因，线程会持续尝试起步。但在很长时间内却无法如愿，甚至永远无法启动。

### 在并发编程中用到的几种执行模型

#### 生产者 - 消费者模型
   <br>一个或多个生产者线程来创建某些工作，并置于缓存或队列中。
   <br>一个或多个消费者线程从队列中获取并完成这些工作。
   <br>生产者和消费者之间的队列是一种限定资源。

#### 读者 - 作者模型
   <br>当存在一个主要为读者线程提供信息源，但只偶尔被作者线程更新的共享资源，吞吐量就会是个问题。
   <br>平衡读者线程和作者线程的需求，提供合理的吞吐量，避免线程饥饿。

#### 宴席哲学家
   <br>想象一下，一群哲学家环坐在圆桌旁。每个哲学家的左手边放了一把叉子。桌面中央摆着一大碗意大利面。
   <br>哲学家们思索良久，直到肚子饿了。每个人都要拿起叉子吃饭。但除非手上有两把叉子，否则就没法进食。
   <br>如果左边或右边的哲学家已经取用一把叉子，中间这位就得等到别人吃完、放回叉子。
   <br>每位哲学家吃完后，就将两把叉子放回桌面，直到肚子再饿。

用线程代替哲学家，用资源代替叉子，就变成了许多企业级应用中进程竞争资源的情形。

如果没有用心设计，这种竞争式系统就会遭遇死锁、活锁、吞吐量和效率降低等问题。

* 警惕同步方法之间的依赖
  <br>避免使用一个共享对象的多个方法。
  <br>有时必须使用一个共享对象的公共方法。这种情况发生时，有 3 种写对代码的手段：
  
    1.基于客户端的锁：客户端代码在调用第一个方法前锁定服务端，确保锁的范围覆盖了调用最后一个方法的代码；
  
    2.基于服务端的锁定：在服务端内创建锁定服务端的方法，调用所有方法，然后解锁。让客户端代码调用这个新方法；
  
    3.适配服务端：创建执行锁定的中间层。这是一种基于服务端锁定的例子，但不修改原始服务端代码；


* 尽可能减小同步区域，否则会会增加资源争用、降低执行效率。


## Smells and Heuristics 味道与启发

### 注释
<br>1.不恰当的信息
<br>2.废弃的注释
<br>3.冗余注释
<br>4.糟糕的注释
<br>5.注释掉的代码
    
### 环境
<br>1.需要多步才能实现的构建
<br>2.需要多步才能做到的测试
 
### 函数
<br>1.过多的参数
<br>2.输出参数,参数用于输入而非输出
<br>3.标识参数,消除布尔值参数
<br>4.死函数,永不被调用的方法应该丢弃
    
### 一般性问题

1. 一个源文件存在多种语言
    <br>应该尽量减少源文件中额外语言的数量和范围。

2. 明显的行为未被实现
    <br>遵循“最小惊异原则”（The Principle of Least Surprise）[2]，函数或类应该实现其他程序员有理由期待的行为。

3. 不正确的边界行为
    <br>追索每种边界条件，并编写测试。

4. 忽略安全
    <br>手工控制 serialVersionUID、关闭某些编译器警告（或者全部警告！）、关闭失败测试都有风险。

5. 重复
    <br>DRY 原则（Don‘t Repeat Yourself，别重复自己）。
    <br>每次看到重复代码，都代表遗漏了抽象。
    <br>在不同模块中不断重复出现、检测同一组条件的 switch / case 或 if / else 链。可以用多态来代替之。
    <br>采用类似算法但具体代码行不同的模块。这也是一种重复，可以使用模版方法模式或策略模式来修正。

6. 在错误的抽象层级上的代码
    <br>所有较低层级概念放在派生类中，所有较高层级概念放在基类中。
    <br>细节实现有关常量、变量或工具函数不应该出现在基类中。基类应该对这些东西一无所知。

7. 基类依赖于派生类
    <br>这样，如果看到基类提到派生类名称，就可能发现了问题。

8. 信息过多
    <br>设计良好的模块有着非常小的接口，并不提供许多需要依赖的函数，耦合度低。
    <br>类中的方法越少越好。函数知道的变量越少越好。类拥有的实体变量越少越好。
    <br>隐藏你的数据。隐藏你的工具函数。隐藏你的常量和你的临时变量。
    <br>不要创建拥有大量方法或大量实体变量的类。不要为子类创建大量受保护变量和函数。
    <br>尽力保持接口紧凑。通过限制信息来控制耦合度。

9. 死代码
    <br>死代码就是不执行的代码。
    <br>可以在检查不会发生的条件的 if 语句中找到。可以在从不抛出异常的 try 语句的 catch 块中找到。
    <br>可以在从不被调用的小工具方法中找到，也可以在永不发生的 switch / case 条件中找到。

10. 垂直分隔
    <br>变量和函数应该在靠近被使用的地方定义。
    <br>本地变量应该正好在其首次被使用的位置上面声明，垂直距离要短。本地变量不该在其被使用之处几百行以外声明。
    <br>私有函数应该刚好在其首次被使用的位置下面定义。私有函数属于整个类，但我们还是要限制调用和定义之间的垂直距离。 

11. 前后不一致
     <br>如果在特定函数中用名为 response 的变量来持有 HttpServletResponse 对象，则在其他用到 HttpServletResponse 对象的函数中也用同样的变量名。
     <br>如果将某个方法命名为 processVerificationRequest，则给处理其他请求类型的方法取类似的名字，例如 processDeletionRequest。
     <br>前后一致，一旦坚决贯彻，能让代码更易于阅读和修改。

12. 混淆视听
      <br>移除没有实现的默认构造器，没有用到的变量，从不调用的函数，没有信息量的注释，等等。

13. 人为耦合
      <br>不互相依赖的东西不该耦合。
      <br>人为耦合是指两个没有直接目的之间的模块的耦合。其根源是将变量、常量或函数不恰当地放在临时方便的位置。
      <br>例如，普通的 enum 不应该在特殊类中包括，因为这样一来应用程序就要了解这些更为特殊的类。
      <br>对于在特殊类中声明一般目的的 static 函数也是如此。

14. 特性依恋
      <br>类的方法只应对其所属类中的变量和函数感兴趣，不该垂青其他类中的变量和函数。
      <br>当方法通过某个其他对象的访问器和修改器来操作该对象内部数据，则它就依恋于该对象所属类的范围。
      <br>它期望自己在那个类里面，这样就能直接访问它操作的变量。

15. Selector Arguments 选择器参数
       <br>选择算子（selector）参数的目的难以记住。每个选择算子参数只是一种避免把大函数切分为多个小函数的偷懒做法。
       <br>选择算子不一定是 boolean 类型。可能是枚举元素、整数或任何一种用于选择函数行为的参数。
       <br>使用多个函数，通常优于向单个函数传递某些代码来选择函数行为。

16. 晦涩的意图
       <br>代码要尽可能具有表达力。联排表达式、匈牙利语标记法和魔术数都遮蔽了作者的意图。

17. 错位的职责
       <br>代码应该放在读者自然而然期待它所在的地方。

18. 不恰当的静态方法
       <br>通常应该倾向于选择用非静态方法。如果有疑问，就使用非静态函数。
       <br>如果的确需要静态函数，就确保没机会让它有多态行为。

19. 使用解释性变量
       <br>解释性变量多比少好。只要把计算过程打散成一系列良好命名的中间值，不透明的模块就会突然变得透明，这很值得注意。

20. 函数名称应该表达其行为

21. 用多态代替 if/else 或 switch/case

22. 用命名常量代替魔术数
       <br>魔术数不仅是说数字。它泛指任何不能自我描述的符号。

23. 避免否定性条件
       <br>否定式要比肯定式难明白一些。所以，尽可能将条件表示为肯定形式。    

24. 不要继承常量
   