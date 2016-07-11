---
layout: post
title: Synthetic Monitoring using Newrelic
---

Prod Monitoring is undoubtedly an emerging area and some of the leading players ihis space are Newrelic, Appdynamics, Compuware and Soasta. Before we delve in to this topic more deeper, I would like to give some background on prod monitoring.

Primarily there are two types of Prod Monitoring:

# 1. Real User Monitoring (a.k.a RUM) : #

In this approach we monitor the real production traffic which is getting genrated at your data centre. Here most of the work done by your server monitoring tool with the help of some agent(java script) which is embedded  in your web pages which helps in relaying performance metrics to a centralizes server. RUM can be applied to any kind of application whether it is a Web, API or Mobile native application. The main advantage of this approach is that you need not to write script for any specific test scenarios. Actual user will navigate through the different functional flows of your application and all the performance metrics will be captured automatically by the tools like newrelic and appdynamics. The biggest problem in relying completely on this approach is that, it will not inform you about any production failures when there is no traffic on your servers. 

Let's take an example, suppose some important component failed in your prod server at late night hours and due to which the login functionality is not working. Due to the off-peak hours, there is no user who is doing login at that time. So RUM will never let you know about this failure till morning when user start using this feature. Here you could waste the crucial hours to fix this problem. To address this problem, we have second type of prod monitoring.

# 2. Synthetic Monitoring: #

This is more of a pro-active monitoring where we keep hitting our production servers periodically to get alerted in case any thing breaks down. It also captures the performance data for this synthetically generated traffic. Other benefits of this approach is that we can monitor even the rarest scenarios where we don't see much production traffic and can also choose the geography of our choice from where we have to hit these transactions.

I have eveluated some of the leading APM tools and found Newrelic really good for Synthetic monitoring.
