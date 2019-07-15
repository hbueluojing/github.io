---
layout: post
title: 初识Reactive Spring
description: 初步了解什么是Reactive Spring
category: blog
---

## What is Reactive Programming ?
> Reactive programming is a declarative programming paradigm concerned with data streams and the propagation of change.    
  
  反应式编程是一种涉及数据流和变化传播的声明式编程范例,使用异步数据流进行编程。 
   
>  Reactive Programming is a programming model that facilitates scalability and stability by creating event-driven non-blocking functional pipelines that react to availability and processability of resources.  
  
  反应式编程是一种编程模型，它通过创建事件驱动的非阻塞功能管道来促进可扩展性和稳定性，这些管道对资源的可用性和可处理性做出反应。 
  
## Why do we use Reactive?
    Because blocking is evil.  
   
### 同步、异步 与阻塞、非阻塞    
    
   __同步异步__ 指的是 __被调用者__ 的消息通知机制，__阻塞非阻塞__ 指的是 __调用者__ 的线程状态。  
   
   以某人用普通水壶和带报警器的水壶烧水为例。    
    
  * 同步阻塞，被调用者没有消息通知机制，调用者线程挂起，除了等待不能做任何事情。     
    某人用普通水壶烧水，等待水开的过程中什么也不能做。     
      
  ![sync-blocking](/hbueluojing.github.io/images/githubpages/sync-blocking.png "同步阻塞")
  
  * 同步非阻塞，被调用者没有消息通知机制，调用者等待返回的同时，线程可以干其他事。    
    某人用普通水壶烧水，等待水开的过程他去看电视了，并且时不时过来看水是否烧开了。    
  
  * 异步阻塞，被调用者使用消息通知返回结果，但是调用者主线程挂起，新线程开销大，复杂。   
    某人用带报警器的水壶烧水，水开后水壶自动报警通知他，但是他除了等什么事也没有做。   
      
  ![asyc-blocking](/hbueluojing.github.io/images/githubpages/asyc-blocking.png "异步阻塞")
 
  * 异步非阻塞，被调用者使用消息通知返回结果，调用者不用等待返回同时线程可以干其他事。  
    某人用带报警器的水壶烧水，他去看电视了，水开后水壶自动报警通知他。  
      
  ![asyc-nonblocking](/hbueluojing.github.io/images/githubpages/async-nonblocking.png "异步非阻塞")

### Java中的三种IO模型

  在Java语言中，一共提供了三种IO模型，分别是阻塞IO（BIO）、非阻塞IO（NIO）、异步IO（AIO）。  
   
  1. BIO （Blocking I/O）：同步阻塞I/O模式，数据的读取写入必须阻塞在一个线程内等待其完成。    
     有一排水壶在烧开水，叫一个线程停留在一个水壶那，直到这个水壶烧开，才去处理下一个水壶。但是实际上线程在等待水壶烧开的时间段什么都没有做。  
    
  2. NIO （New I/O）：同时支持阻塞与非阻塞模式，但主要是使用同步非阻塞IO。      
     叫一个线程不断的轮询每个水壶的状态，看看是否有水壶的状态发生了改变，从而进行下一步的操作。
         
  3. AIO （Asynchronous I/O）：异步非阻塞I/O模型。     
     为每个水壶上面装了一个开关，水烧开之后，水壶会自动通知水烧开了。 

## How to use Reactive with Spring?

  被订阅者 (Publisher) 主动推送数据给 订阅者 (Subscriber)，触发 onNext() 方法。发生异常或调用完成时触发另外两个方法。   
  
  
  被订阅者 (Publisher) 发生异常，则触发 订阅者 (Subscriber) 的 onError() 方法进行异常捕获处理。     
  
  
  被订阅者 (Publisher) 每次推送都会触发一次 onNext() 方法。所有的推送完成且无异常时，onCompleted() 方法将 在最后 触发一次。
  。
  
### Flux 和 Mono
    
  Flux 和 Mono 是 Reactor 中的两个基本概念。
    
#### Flux 
  
  实现了 org.reactivestreams.Publisher 接口，代表 0 到 N 个元素的发表者。在该序列中可以包含三种不同类型的消息通知：正常的包含元素的消息、序列结束的消息和序列出错的消息。
  
  当消息通知产生时，订阅者中对应的方法 onNext(), onComplete()和 onError()会被调用。

#### Mono 

  实现了 org.reactivestreams.Publisher 接口，代表 0 到 1 个元素的 发布者。
  
  该序列中同样可以包含与 Flux 相同的三种类型的消息通知。
  
#### Scheduler
     
  代表背后驱动反应式流的调度器，通常由各种线程池实现。  
  

```
public class JokeService {
	private static final String BASE = "http://api.icndb.com/jokes/random?limitTo=nerdy";
	private RestTemplate restTemplate;
	private WebClient client = WebClient.create("http://api.icndb.com");

	@Autowired
	public JokeService(RestTemplateBuilder builder) {
		restTemplate = builder.build();
	}

	public String getJoke(String first, String last) {
		String url = String.format("%s&firstName=%s&lastName=%s", BASE, first, last);
		JokeResponse response = restTemplate.getForObject(url, JokeResponse.class);
		return Optional.ofNullable(response)
				.map(JokeResponse::getValue)
				.map(Value::getJoke)
				.orElse("");
	}

	public Mono<String> getJokeAsync(String first, String last) {
		String path = "jokes/random?limitTo=nerdy&firstName={first}&lastName={last}";
		return client.get()
				.uri(path, first, last)
				.retrieve()
				.bodyToMono(JokeResponse.class)
				.map(jokeResponse -> jokeResponse.getValue().getJoke());
	}
}

```




