---
layout: post
title: "Integrating Rackspace Auto Scale Groups with ObjectRocket Mongo databases"
date: 2014-12-01 13:00:58 -0500
comments: true
categories: 
- devops
- rackspace
---

Thanks to some pretty awesome support from Jon Fanti and John Moore at [ObjectRocket](http://objectrocket.com),
I learned this week that we had missed two key optimizations for using ObjectRocket MongoDBs with Rackspace
Auto Scaling groups (ASGs).

## ServiceNet

First, ObjectRocket support can provide medium and large customers with a server FQDN that resolves to a
[ServiceNet](http://www.rackspace.com/knowledge_center/frequently-asked-question/what-is-servicenet) private IP.
You can use this FQDN instead of the server name shown in the connect string for your instance.  As long
as your cloud servers and ObjectRocket are in the same Rackspace data center, the ServiceNet connection string
will avoid data transfer charges and keep your packets from transiting the public Internet.

## Dynamic IP ACLs

We struggled to manually maintain the list of authorized IPs for our ObjectRocket MongoDB instances
when a ASG would add a new node.  We had a backlog plan to script the IP ACLs using Chef, but, hadn't
found the time yet.

Fortunately, ObjectRocket already supports this!  See https://app.objectrocket.com/external/rackspace

![Screenshot of ObjectRocket integration with Rackspace](/images/2014-12-01.png)

According to John, the ObjectRocket integration with your Rackspace Cloud account will automatically sync
the IP ACLs with your list of current Cloud VMs.  Moreover, the integration will ignore any manual IP ACLs
you create (as long as your description doesn't use the `rax-` prefix).
  
