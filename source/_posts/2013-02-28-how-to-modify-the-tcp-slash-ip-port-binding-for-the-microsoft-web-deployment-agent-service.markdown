---
layout: post
title: "How to modify the TCP/IP port binding for the Microsoft Web Deployment Agent Service"
date: 2013-02-28 16:44
comments: true
categories:
- webdeploy
- msdeploy 
- iis
---

I love shell scripting, which is probably why I seem to somehow get organically involved in "DevOps" on most of my project work.  I'm drafting up a series of posts on tips and tricks for shell scripts (with love for both Windows and *nix) - it seems to be a fading art among recent comp sci grads.  Until I finish those posts, I wanted to share a quick script I wrote to reassign the TCP/IP port binding for Microsoft's Web Deployment Agent service.

This is my attempt to refine a very good answer posted a couple years ago [asking if web deploy can run on a port other than 80](http://stackoverflow.com/questions/5867392/can-the-web-deploy-agent-run-on-a-port-other-than-80-on-iis6).  Why, yes it can...

{% gist 5060329 %}
