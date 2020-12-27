---
layout: post
title:  "Configmap Reload with Spring Boot in Kubernetes"
date:   2020-12-28 01:38:27 +0300
categories: article
---
In Kubernetes, configmap is used for keeping properties for an application. Once Spring applications required restart just due to change in properties file to take effect.
Now in cloud with the help of fabric8 Kubernetes client, restart is not required anymore. We can reflect changes in configuration to Spring Boot live without downtime.

There are two reload modes: polling and event. Polling as the name implies polls Kubernetes API periodically. Event is more effective since it takes effect when configmap changed. 
We will cover event mode reload in this post.


How events arrive Spring Boot? Does Kubernetes aware of our Spring Boot app? No. Don't confuse this event with Kubernetes event.

![configmap-diagram](/assets/configmap-diagram.png)
<br/>
 
Above you can see Websocket is used between Spring Boot and Kubernetes API Server. Assuming we are using namespace "default" is this example, changes to response in this address reflected to KubernetesClient https://{api_server_clusterip}/api/v1/namespaces/default/configmaps/k8s-live-reload-configmap . 
If kube-proxy does not allow WebSocket then API Server is polled by HttpClient.

In Spring side, bootstrap.yaml reload mode and configmap name given. I deliberately disabled actuator endpoints, because most examples include actuator endpoints, however it is not required because fabric8-client refreshes bean internally.
In pom.xml however actuator dependencies needed since fabric8-config depends on actuator configuration classes.
{% highlight yml %}
spring:
  application:
    name: k8s-live-reload-example
  cloud:
    kubernetes:
      reload:
        enabled: true
        mode: event
      config:
        sources:
          - name: k8s-live-reload-configmap
          
management:
  endpoints:
    enabled-by-default: false #actuator endpoints disabled in order to show it is not required to reload config
{% endhighlight %}

Beans annotated with @ConfigurationProperties are refreshed.

{% highlight java %}
@Configuration
@ConfigurationProperties(prefix = "bean" )
@Data
public class Config {

    private String testvalue;
    
}
{% endhighlight %}

 
<br><b>Run example</b><br>
We need to build and push our application image to Docker and Kubernetes need to see this image. I suggest using Minikube 1.16.0. There are problems in prior versions due to DNS and VM(Hyper-V, VirtualBox). This version allows to run minikube as a docker container.
It can be download from here: <a class="text-accent" href="https://github.com/kubernetes/minikube/releases" >https://github.com/kubernetes/minikube/releases</a> Assuming you are not behind proxy or VPN (you may have network problems), following installation start minikube with this command.
You must have docker running.
{% highlight shell %}
minikube start --driver=docker
{% endhighlight %}
This will download images and start minikube. Then install kubectl: <a class="text-accent" href="https://kubernetes.io/docs/tasks/tools/install-kubectl" >https://kubernetes.io/docs/tasks/tools/install-kubectl</a>

First we need rolebinding for querying API Server. Otherwise KubernetesClient cannot reach API Server. default is the namespace here.
{% highlight shell %}
kubectl create rolebinding default-sa-view --clusterrole=view --serviceaccount=default:default --namespace=default
{% endhighlight %}

Get example project
{% highlight shell %}
git clone https://github.com/gungor/kubernetes-examples.git
cd kubernetes-examples/springboot-k8s-configmap-reload-example
{% endhighlight %}

Now add configmap named "k8s-live-reload-configmap" to kubernetes
{% highlight shell %}
kubectl apply -f src/k8s/config-map.yml
{% endhighlight %}

Switch to kubernetes docker daemon in active shell to make Kubernetes reach application image. Run the command below, then run the last command in the output.
{% highlight shell %}
minikube docker-env
{% endhighlight %}

Build application image
{% highlight shell %}
mvn clean install spring-boot:build-image
{% endhighlight %}

Deployment and service
{% highlight shell %}
kubectl create deployment liveconfig-demo --image=springboot-configmap-livereload-example:0.0.1-SNAPSHOT
kubectl create service clusterip liveconfig-demo --tcp=8080:8080
{% endhighlight %}

<br><b>Test</b><br>

Now see config before it has changed
Deployment and service
{% highlight shell %}
kubectl get service liveconfig-demo #copy CLUSTER-IP returned from this command
minikube ssh
curl http://CLUSTER-IP:8080/liveconfigtest
{% endhighlight %}

It should return oldvalue. Now edit config map through command below or through minikube dashboard
{% highlight shell %}
kubectl edit configmap k8s-live-reload-configmap
{% endhighlight %}

After editing configmap, recheck application
{% highlight shell %}
minikube ssh
curl http://CLUSTER-IP:8080/liveconfigtest
{% endhighlight %}

It returns new value. 





