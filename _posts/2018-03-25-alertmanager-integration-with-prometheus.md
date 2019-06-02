---
layout: post
title:  "Alertmanager Integration with Prometheus"
date:   2018-03-25
desc: "How to integrate Prometheus and Alertmanager"
keywords: "Linux,blog,DevOps,Prometheus,Monitoring,Alertmanager"
categories: [Monitoring]
tags: [Linux,blog,DevOps,Prometheus,Monitoring,Alertmanager]
icon: icon-html
---

One day I got a call from one of my friend and he said to me that he is facing difficulties while setting up AlertManager with Prometheus. Then, I observed that most of the people face such issues while establishing a connection between AlertManager and receiver such as E-mail, Slack etc.

From there, I got motivation for writing this blog so AlertManager setup with Prometheus will be a piece of cake for everyone.

If you are new to AlertManager I would suggest you go through with our Prometheus blog.

## What Actually AlertManager is?
AlertManager is used to handle alerts for client applications(like Prometheus). It also takes care of alerts deduplicating, grouping and then routes them to different receivers such as E-mail, Slack, Pager Duty.

In this blog, we will only discuss on Slack and E-mail receivers.

AlertManager can be configured via command-line flags and configuration file. While command line flags configure system parameters for AlertManager, the configuration file defines inhibition rules, notification routing, and notification receivers.

## Architecture
Here is a basic architecture of Alertmanager with Prometheus.

This is how Prometheus architecture works:-

<p align="center">
<img src="{{ site.img_path }}/monitoring/alertmanager.png" width="75%">
</p>

<br/><br/>

- If you see in the above picture Prometheus is scraping the metrics from its client application(exporters).
- When the alert is generated then it pushes it to the AlertManager, later AlertManager validates the alerts groups on the basis of labels.
- and then forward it to the receivers like Email or Slack.

If you want to use a single AlertManager for multiple Prometheus server you can also do that. Then architecture will look like this:-

<p align="center">
<img src="{{ site.img_path }}/monitoring/alertmanager1.png" width="75%">
</p>

<br/><br/>

## Installation
Installation part of AlertManager is not a fancy thing, we just need simply to download the latest binary of AlertManager from **[here](https://github.com/prometheus/alertmanager/releases)**.

```bash
cd /opt/
wget https://github.com/prometheus/alertmanager/releases/download/v0.11.0/alertmanager-0.11.0.linux-amd64.tar.gz
```

After downloading, let’s extract the files.

```bash
tar -xvzf alertmanager-0.11.0.linux-amd64.tar.gz 
```
