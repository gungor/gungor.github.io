---
layout: post
title:  "Arquillian brings nothing new"
date:   2018-12-19 14:06:54 +0300
categories: article
---
Arquillian is a test-framework that helps you to run JUnit tests (or any other test frameworks) on remote Test or Production environments. That is something when you think mocks are useless especially in Integration Testing. 
How does arquillian work? It generates an archive file (war,ear,etc) in order to includes jar files that you specified or it uses and deploys existing archive file to the application server. 
Then arquillian runs tests with the help of configured ArquillianServlet over Http then reflects results to your local IDE (e.g Intellij IDEA). At first sight, it seems useful. However there are several burdens of using arquillian.

- Upload duration of an archive file to remote server. If your upload speed is low, it is a real problem.
- There is few documentation about arquillian.
- Generally reflects same error on local side. Even if it is about NoClassDefFoundError or Spring Context creation exceptions. You need to look in server logs to understand the real problem.

If you have somehow handled these issues, you can go ahead with arquillian.
After these problems, I left trying arquillian and run remote tests with JUnitCore like it is shown below:

{% highlight perl %}
java -cp ... org.junit.runner.JUnitCore -junit4 com.gungor.test.RemoteTest
{% endhighlight %}