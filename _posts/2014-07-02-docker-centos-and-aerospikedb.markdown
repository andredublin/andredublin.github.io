---
layout: post
title: "Docker, CentOS, and Aerospike DB"
date: 2014-07-02
categories: architecture
image: /assets/images/cover.jpg
---

My recent endeavors with Docker have been quite rewarding.  From creating a MongoDB cluster, to a Nginx load balanced reverse proxy container that routes an Angularjs container with a Nodejs API container backing that up. Theres definitely no looking back to provisioning VMs alone anymore.

But recently a new database has piqued my interests [Aerospike](http://www.aerospike.com/).  So with this new NoSQL DB I wanted to run it within a CentOS container.  However there are some current problems that you may run into until the [CentOS 6.6](https://bugs.centos.org/view.php?id=7126#c19831) base image is released on the public docker registry.

Primarily the issues that arise are the package dependencies that Aerospike and the Aerospike documentation call for.
Here is the example from my current Dockerfile

{% gist andredublin/f4fdf7a0ce41ab0481cb %}

Starting at the ```Install necessary software and dependencies``` comment I install wget, tar, logrotate, sudo, and the final odd command system-config-services.
This is where you may run into trouble since the documentation requires you to start your Aerospike with the command ```sudo service aerospike start``` and you get centos complaining that sudo or service doesn't exist.

But those final two commands will help resolve this issue.  If you notice that the ```system-config-services``` command is prepended with ```--enablerepo centosplus``` this allows yum to get packages from the CentOSPlus repository.

Now CentOS recommends that when you require dependencies from that repository to only enable it when you can to pick the exact package that you want to use, vs. enabling it in your ```/etc/yum.repos.d/``` since it is not part of the CentOS upstream distribution.  So consider this fix to be experimental until the updated base image is released.

I plan on following up with a demostration of an Aerospike cluster using containers so stay tuned.

## Comments