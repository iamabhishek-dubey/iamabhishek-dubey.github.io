---
layout: post
title:  "Redis Best Practices and Performance Tuning"
date:   2019-04-20
desc: "How to tune Redis Performance"
keywords: "Linux,Shell,blog,Script,DevOps"
categories: [Middleware]
tags: [Linux,Redis,Performance,Middleware,DevOps,Database]
icon: i
---

One of the thing that I love about my organization is that you don’t have to do the same repetitive work, you will always get the chance to explore some new technologies. The same chance came across to me a few days back when one of our clients was facing the issue with Redis.

They were using the Redis Cluster with Sentinel for which they were facing issue regarding performance, whenever the connection request was high the Redis Cluster was not able to bear the load.

Since they were using a decent configuration of the server in terms of CPU and Memory but the result was the same. So now what????

The Answer was to tune the performance.

There are plenty of Redis performance articles out there, but I wanted to share my experience as a DevOps with Redis by creating an article which will include the most essential and important stuff that is needed for a Developer or a DevOps Engineer.

**So let’s get started.**

<br/><br/>

## TCP-KeepAlive

Keepalive is a method to allow the same TCP connection for HTTP conversation instead of opening a new one with each new request.

In simple words, if the keepalive is off the Redis will open a new connection for every request which will slow down its performance. If the keepalive is on then Redis will use the same TCP connection for requests.

Let’s see the graph for more details. The Red Bar shows the output when keepalive is on and Blue Bar shows the output when keepalive is off

<p align="center">
<img src="{{ site.img_path }}/redis/redis-first.png" width="75%">
</p>

<br/><br/>

For enabling the TCP keepalive, Edit the redis configuration and update this value.

```shell
vim /etc/redis/redis.conf
# Update the value to 0
tcp-keepalive 0
```

<br/><br/>

## Pipelining

This feature could be your lifesaver in terms of Redis Performance. Pipelining facilitates a client to send multiple requests to the server without waiting for the replies at all and finally reads the reply in a single step.

For example:-

<p align="center">
<img src="{{ site.img_path }}/redis/redis-second.png" width="75%">
</p>

You can also see in the graph as well.

<p align="center">
<img src="{{ site.img_path }}/redis/redis-third.png" width="75%">
</p>

Pipelining will increase the performance of redis drastically.

<br/><br/>

## Max-Connection
Max-connection is the parameter in which is used to define the maximum connection limit to the Redis Server. You can set that value accordingly (Considering your server specification) with the following steps.

```shell
sudo vim /etc/rc.local

# make sure this line is just before of exit 0.
sysctl -w net.core.somaxconn=65365
```
This step requires the reboot if you don’t want to reboot the server execute the same sysctl command on the terminal itself.

<br/><br/>

## Overcommit Memory
Overcommit memory is a kernel parameter which checks if the memory is available or not. If the overcommit memory value is 0 then there is a chance that your Redis will get OOM (Out of Memory) error. So do me a favor and change its value to 1 by using the following steps

```shell
echo 'vm.overcommit_memory = 1' >> /etc/sysctl.conf
```

<br/><br/>

## RDB Persistence and Append Only File

RDB persistence and Append Only File options are used to persist data on disk. If you are using the cluster mode of Redis then the RDB persistence and AOF is not required. So simply comment out these lines in redis.conf

```shell
sudo vim /etc/redis/redis.conf

# Comment out these lines
save 900 1
save 300 10
save 60 10000

rdbcompression no
rdbchecksum no

appendonly no
```

<br/><br/>

## Transparent Huge Page(THP)

Most of the people are not aware of this term. Basically, For making the translation of physical and virtual memory kernel uses the concept of paging. This feature was defined to enhance the memory mapping process but somehow it slows down the databases which are memory based (for example — in the case of Redis). To overcome this issue you can disable THP.

```shell
sudo vim /etc/rc.local

# Add this line before exit 0
echo never > /sys/kernel/mm/transparent_hugepage/enabled
```

As graph also shows the difference in performance. The Red Bar is showing THP disabled performance and Blue Bar is showing THP disabled performance.

<p align="center">
<img src="{{ site.img_path }}/redis/redis-fourth.png" width="75%">
</p>

<br/><br/>

## Some Other Basic Measures in Redis Configuration

|Config Option|Value|Description|
|-------------|-----|-----------|
|maxmemory|70% of the system|maxmemory should be 70 percent of the system so that it will not take all the resource of the server|
|maxmemory-policy|volatile-lru|It adds a random key with an expiry time|
|loglevel|notice|Loglevel should be notice so that log will not take too much resource|
|timeout|300|There should be a timeout value as well in redis configuration which prevents redis from spending too much time on the connection. It closes the connection of the client if it is ideal for more than 300 seconds.|

<br/><br/>

So now your redis is ready to give a killer performance. In this blog, we have discussed redis best practices and performance tuning.

There are multiple factors which are yet to be explored to enhance the performance of Redis if you find that before I do, please let me know to improve this blog.

**In my next blog, I will discuss around how can we do Redis Performance Testing and how we are doing it in our Organisation.**
