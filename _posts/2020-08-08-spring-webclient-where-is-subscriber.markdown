---
layout: post
title:  "Using Spring Reactive WebClient instead of RestTemplate"
date:   2020-08-08 14:02:13 +0300
categories: article
---
In the age of where resources are cheap, applications running on Prod environment still have resource problems (CPU, Memory). Threads were being used for parallel processing
and are useful. However when they are used inefficiently, it still causes resource problem. 

An example is when you call a webservice. As you wait for the response, your thread is blocked.
{% highlight java %}
RestTemplate restTemplate = new RestTemplate();
HttpEntity<Request> request = new HttpEntity<>(new Request("test"));
restTemplate.postForObject("http://localhost:8080/testService", request, Request.class);
{% endhighlight %}

If RestTemplate called in a separate thread, you will need to wait for this Threads to complete as much as RestTemplate receives a response from IO operation.
Consider that, you need to call 1000 times an HTTP service concurrently. You need 1000 Threads to run and wait for its RestTemplate blocking call.
It will degrade significantly performance of your application.

Reactive clients (WebClient) brings a solution to that increasing number of thread problem. WebClient is a part of Spring Reactor project. 
Spring Reactor uses Event-Loop mechanism by using fixed number of threads. As the number of your http calls increase fixed number of threads (number of cores by default)
handle IO operations.

{% highlight java %}
HttpEntity<Request> request = new HttpEntity<>(new Request("test"));
 webClient.post()
                .uri( "http://localhost:8080/testService" )
                .contentType(MediaType.APPLICATION_JSON)
                .body(Mono.just(request), Request.class )
                .exchange()
                .doOnSuccess( (ClientResponse clientResponse) -> {
                    // response can be processed here asynchronously
					// one of reactor-http-nio handles this block
                })
                .doOnError( (Throwable error) -> {
                    // error can be processed here asynchronously
					// one of reactor-http-nio handles this block
                })
                .subscribe();
{% endhighlight %}
