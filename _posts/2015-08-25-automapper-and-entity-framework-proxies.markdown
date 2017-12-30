---
layout: post
title: "Automapper and Entity Framework Proxies"
date: 2015-08-25
categories: .net
image: /assets/images/cover.jpg
---

I’ve recently learned about Automapper for mapping objects to other objects and I couldn’t be happier with the results from its use in my day to day projects.  However I did come across a situation where I needed to map POCOs to entity objects.  What I was trying to do is map the properties of the POCO to an entity that needed to be modified and saved during an update operation within my Web API project.  So using the familiar:

{% gist andredublin/43528bea0caeed93de87 %}

However I discovered that Automapper was removing my retrieved entity object from the object graph that entity framework provides us in order to track changes of the object state.  This of course was not acceptable and I didn’t want to have us go through and map the properties of POCO to entity by hand.

Now I could go and call the static Mapper class and define the types of the objects every time I need to map from POCO to entity making use of typeof:

{% gist andredublin/429102131c38410fd139 %}

But this isn’t a great way of enabling these mappings, plus its a lot of typing, so I created a helper method that wraps this logic and makes use of generics types instead of using typeof as parameters of the method:

{% gist andredublin/90340f0a9c5d51d1dd2e %}

## Comments