---
layout: post
title: "Nginx and Angular url routing"
date: 2014-08-18
categories: javascript
comments: true
image: /assets/images/cover.jpg
---

Lately I've had the opportunity to work with Angular on a regular basis. At first it has been a challenge due to a large learning curve with the framework. But after a couple projects things have been moving smoothly. Anyway this post is a blurb on how to properly route request from Nginx to your Angular application.

Lots of resources recommend the follow Nginx configuration for your application.

{% gist andredublin/6c028fe1aba4bf7c21dc %}

Note that this is working with an Angular app that is running in html5mode.

So in our Nginx conf we have our root location try_files directive that checks for the existence of files in order, and then returns the first file that is found.

Now this gets us 50% of the way there. What happens when you have nested routes? e.g. foobar.com/fizz/buzz or query strings? e.g. foobar.com/fizz/buzz?ping=pong.

Well Nginx gets you part of the way there with query strings but not nested routes. After some hours of reading Nginx documentation and searching for answers, I came across this solution.

In you Angular application entry point file, usually index.html. Inside the head tag section place this tag, ```<base href="/">```

This will allow Angular to handle all route request properly.
