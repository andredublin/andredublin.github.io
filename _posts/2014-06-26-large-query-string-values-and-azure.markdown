---
layout: post
title: "Large query string values and Azure"
date: 2014-06-26
comments: true
categories: .net
---

By default Microsoft IIS accepts a maximum query string length of 2048 characters.  If there is a query string received by the IIS with more than 2048 characters, it will throw a 404.15 - Query String too long error.

If your running a service on Azure and are running into issues sending very large values over http via a get request.  Then you will have to set the maxQueryString property value to compensate for your request.

Unfortunately many answers to this problem fail to address when working on an azure instance.  In that context you will need to upload a Web.config file to the wwwroot folder of your instance. Under the system.webServer, security section, add the requestFiltering section.  Under the requestFiltering section, add the requestLimits tag with your desired maxQueryString value.  Now your web service or web site will be able to accept a maximum query string length you desire.  In the example below I have it set to accept a maxQueryString value of "10000" characters.  Even if you set a big value for maximum query string, there is a limit for each browser which is handling the url and the query string.

{% gist andredublin/802d5c0596e2a3275fbe %}

The best practice is to limit the maxQueryString size as much as possible, to avoid any injection attacks. You can give a value based on your requirements, but keep in mind that alllowing long query strings is a potential security risk and bad design.
