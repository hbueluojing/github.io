---
layout: post
title: 初识Reactive Spring
description: 初步了解什么是Reactive Spring
category: blog
---

## What is Reactive Programming ?
> Reactive programming is a declarative programming paradigm concerned with data streams and the propagation of change. 
    
    反应式编程是一种涉及数据流和变化传播的声明式编程范例。
    反应式编程,使用异步数据流进行编程。


## Why do we use Reactive?
    Because blocking is evil.
    
  同步阻塞，app除了等待不能做任何事情
  
  ![sync-blocking](/hbueluojing.github.io/images/githubpages/sync-blocking.png "同步阻塞")
  
  异步阻塞，新线程开销大，复杂
  
  ![asyc-blocking](/hbueluojing.github.io/images/githubpages/asyc-blocking.png "异步阻塞")
 
  异步非阻塞
  
  ![asyc-nonblocking](/hbueluojing.github.io/images/githubpages/async-nonblocking.png "异步非阻塞")

## How to use Reactive with Spring?

### Flux 和 Mono
    Flux 和 Mono 是 Reactor 中的两个基本概念。
    
#### Flux 
    表示的是包含 0 到 N 个元素的异步序列。在该序列中可以包含三种不同类型的消息通知：正常的包含元素的消息、序列结束的消息和序列出错的消息。
    当消息通知产生时，订阅者中对应的方法 onNext(), onComplete()和 onError()会被调用。

#### Mono 
    表示的是包含 0 或者 1 个元素的异步序列。该序列中同样可以包含与 Flux 相同的三种类型的消息通知。


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




