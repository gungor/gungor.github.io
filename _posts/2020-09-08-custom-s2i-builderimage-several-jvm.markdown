---
layout: post
title:  "Building custom Openshift S2I Builder image with Java 11,13,14"
date:   2020-09-08 03:56:47 +0300
categories: article
---
In the age of containers and Kubernetes, applications needed to be configured with Dockerfiles and YAMLs. That can be another workload for developers and devops.
Openshift provide a tool known as source-to-image (S2I) in order to simplify image creation from application sources. No additional configuration files are needed in this process.
It takes application source and produces a runnable app in Openshift image registry or in external image registry. Then runs this image in a container. Let's have a look at the command below.

{% highlight shell %}
oc new-app https://github.com/gungor/s2i-springboot-sample.git -i openjdk-11-rhel7
{% endhighlight %}

Assuming openjdk-11-rhel7 exists in your Openshift image registry, which can be pulled from Redhat Container Catalog (https://catalog.redhat.com), 
this command tells Openshift to take application source from git repository and use openjdk-11-rhel7 as builder image to produce application image and other resources such as deploymentconfig,
buildconfig and service.

We used openjdk-11-rhel7 here. What if Java 13,14 needed? What if OpenJ9 needed due to memory limits? You may not find the exact image you need in Redhat Container Catalog.
Then you can produce your own builder image. 

S2I builder images have assemble and run scripts. These scripts are must for a builder image. I will show how a custom builder image is built.

<br><b>assemble</b>


Following git clone,
build pod runs sti-build container

- pulls image openjdk-11-rhel7 then runs a container (if incremental build is specified, pulls application image which was built from openjdk-11-rhel7 )
- application source was cloned with init container and assemble script runs /usr/local/s2i/assemble to compile, build and package your application. 
- commits new application image (s2i-springboot-sample-1) to image registry 

* if incremental build is used maven artifacts are extracted from previous image and those are not downloaded again. Thus you save build time.

<br><b>run</b>


application image runs in a pod and runs script /usr/local/s2i/run

<br><b>Building custom builder image</b>


This guilde is to show you how you make changes in a builder image: <a class="text-accent" href="https://github.com/gungor/s2i-builder-image">https://github.com/gungor/s2i-builder-image</a> .
You can also setup any environment for any programming language. 
I used OpenJDK 14 with OpenJ9 and maven 3.6.3. 
Openshift <a class="text-accent" href="https://github.com/jboss-openshift/cct_module">cct_module</a> has many scripts helping builder image. assemble, run, save-artifacts files and configuration files can be found there.
I made minimum changes referencing openjdk-11-rhel7 image. Because several configuration files exists in cct_module and they use Openshift specific environment variables.
Jvm start parameters adjusted by additional scripts in cct_module.
Keeping them is useful. Dockerfile can be simplified though, since it contains several redundant files.

{% highlight shell %}
# install maven
RUN mkdir /tmp/tools && \
	curl -o /tmp/tools/apache-maven-3.6.3-bin.tar.gz  -L maven_download_url && \ 
	tar -zxvf /tmp/tools/apache-maven-3.6.3-bin.tar.gz -C /tmp/tools && \ 
	mkdir /usr/lib/maven && \ 
	mv /tmp/tools/apache-maven-3.6.3/* /usr/lib/maven && \ 
	rm /tmp/tools/apache-maven-3.6.3-bin.tar.gz
ENV PATH="/usr/lib/maven/bin:${PATH}"

# install java
# openj9 java:14
RUN curl -o /tmp/tools/jdk.tar.gz -L jdk_download_url && \ 
	tar -zxvf /tmp/tools/jdk.tar.gz -C /tmp/tools && \ 
	mkdir /usr/lib/jvm && \ 
	mv /tmp/tools/jdk-14.0.2+12/* /usr/lib/jvm && \ 
	rm /tmp/tools/jdk.tar.gz
	
ENV PATH="/usr/lib/jvm/bin:${PATH}"
ENV JAVA_HOME="/usr/lib/jvm" 
{% endhighlight %}

Changes I made seen above
- maven setup
- jdk setup

<br><b>Minimum requirements for Builder Image</b>
- assemble script in /usr/local/s2i
- run script in /usr/local/s2i

<br><br><a class="text-accent" href="https://github.com/gungor/s2i-builder-image">https://github.com/gungor/s2i-builder-image</a><br>
Run <a class="text-accent" href="https://github.com/gungor/s2i-builder-image/blob/master/build.sh">build.sh</a>, then you have a builder image in your docker registry. You can customize maven and jdk version in Dockerfile.  

This guide is to just give you an idea about how builder images work and produced. Production usage of this image is not recommended.

















