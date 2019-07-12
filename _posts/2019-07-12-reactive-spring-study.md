---
layout: post
title: 初识Reactive Spring
description: 初步了解什么是Reactive Spring
category: blog
---

## What is Reactive Programming ?
响应式编程
***
概念不重要！


## Why do we use Reactive?
Because blocking is evil.
***
当主线程的I/O阻塞时，
<ul>
   <li> 同步阻塞，app除了等待不能做任何事情</li>
   <li>异步阻塞，新线程开销大，复杂</li>
</ul>

> 异步非阻塞

## How to use Reactive with Spring?
Mono
***
Flux
***

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



