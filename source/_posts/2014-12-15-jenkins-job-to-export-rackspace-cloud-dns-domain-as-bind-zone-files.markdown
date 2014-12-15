---
layout: post
title: "Jenkins Job to export Rackspace Cloud DNS Domain As BIND Zone Files"
date: 2014-12-15 09:39:33 -0500
comments: true
categories: 
- devops
- rackspace
- dns
- jenkins
---

Rackspace Cloud DNS offeres a great web console, along with a solid API for managing
DNS records dynamically from CM tools like Chef.

The web UI @ https://mycloud.rackspace.com doesn't (yet) suppport an action to
export your domain(s) to standard BIND format zone files.

However, the API does support zone file exports,
[`GET /v1.0/{account}/domains/{domainId}/export`](http://docs.rackspace.com/cdns/api/v1.0/cdns-devguide/content/GET_exportDomain_v1.0__account__domains__domainId__export_domains.html).

I wanted to create a scheduled Jenkins job to export a domain managed by
Cloud DNS to GitHub for both versioning and disaster recovery.

One gotcha with the API is it's asynchronous - you request an export, then
periodically poll on the status of the export.  The API also has rate limiting.
So, the export is a bit more involved than a simple `curl` call.

Based on [this Rackspace community support post](https://community.rackspace.com/products/f/25/t/1743),
I found a great python utility, [clouddns.py by Wichert Akkerman](https://github.com/wichert/clouddns).

>Note: I couldn't use the https://github.com/rackspace/pyrax official SDK,
as I'm on CentOS 6.5 with Python 2.6, and the SDK requires Python 2.7.  I also tried
the [gist by DavidWittman](https://gist.github.com/DavidWittman/4727690) but failed
to get it working with the LON region despte following
[the clouddns README](https://github.com/rackerlabs/python-clouddns/blob/master/README.rst)

Here's the basis of the script I used in a Jenkins job to export a domain and subdomains
every 15 minutes, alongw with the Git publisher for Jenkins to push the changes back to
a GitHub repo.

{% gist 14dddef6fa0318761e3c %}

