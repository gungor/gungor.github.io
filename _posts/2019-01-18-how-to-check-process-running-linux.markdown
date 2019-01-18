---
layout: post
title:  "Check Process Status Using Parameters in Linux"
date:   2019-01-18 13:24:26 +0300
categories: article
---
Whe you have a lot of processes running in your machine, you often check your if application running using ps command in linux for example (java processes)
{% highlight perl %}
ps aux | grep java
{% endhighlight %}

That command outputs several running java processes on your linux machine. However it is still difficult to see if your application running, if you search certain parameters by your eye.
Below is a sample bash script that helps you to find if your application running by checking parameters.

{% highlight perl %}
#!/usr/bin/bash
echo "Checking application"

TEMP_RESULT_FILE=/etc/temp_$$_result

check_app_pid(){
ps aux | grep java | grep -v grep | awk '{print$2}' | while read pid 
do
	ps -fp $pid > ${TEMP_RESULT_FILE} 
	if [ -n "`grep ${1} ${TEMP_RESULT_FILE}`" ]; then
		echo $pid > ${TEMP_RESULT_FILE} 
		return 1
	fi
done
}

running_result(){
	echo "${1} running with pid=$(cat ${TEMP_RESULT_FILE})"
}

not_running_result(){
	echo "${1} not running."
}

check_node(){
	result=$(check_app_pid ${1})
	if [[ $? -eq 1 ]]; then
		running_result ${2}
	else
		not_running_result ${2} 
	fi
}

check_node karaf.home=/u01/test TEST_APP
check_node config.dir=/u01/test/etc/test1 TEST1
check_node config.dir=/u01/test/etc/test2 TEST2
 
rm -rf ${TEMP_RESULT_FILE} 

{% endhighlight %}

After you run this script, it shows an output like

{% highlight perl %}
TEST_APP running with pid=77495
TEST1 running with pid=861
TEST2 running with pid=891
{% endhighlight %}