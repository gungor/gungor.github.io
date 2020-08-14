---
layout: post
title:  "Performance advantages of Spring Reactive WebClient over RestTemplate"
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

If RestTemplate called in a separate thread, you will need to wait for this thread to complete as long as RestTemplate receives a response from IO operation.
Consider that, you need to call 1000 times an HTTP service concurrently. You need 1000 Threads to run and wait for its RestTemplate blocking call.
It will degrade significantly performance of your application.

Reactive clients (WebClient) brings a solution to that increasing number of thread problem. WebClient is a part of Spring Reactor project. 
Spring Reactor uses Event-Loop mechanism by using fixed number of threads. As the number of your http calls increase fixed number of threads (number of cores by default)
handle IO operations.

As it is shown below, if webclient is used reactor-http-nio threads are created as many as the number of cpus in your machine. 
If you have 4 cores, 4 io.netty.channel.nio.NioEventLoop instances and 4 reactor-http-nio threads are created and their number remain fixed even if concurrent WebClient calls increase.
You can see reactor-http-nio threads in VisualVM.

![Reactor nio threads](/assets/reactor-nio-threads.PNG)
<br/><br/><br/>


Here, Netty is used in Spring Reactor. Netty is responsible for handling non blocking http calls. There also alternatives such as Jetty, Undertow, vs.
Success and error callbacks are handled in reactor-http-nio threads.

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


