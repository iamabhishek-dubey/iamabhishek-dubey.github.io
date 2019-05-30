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
