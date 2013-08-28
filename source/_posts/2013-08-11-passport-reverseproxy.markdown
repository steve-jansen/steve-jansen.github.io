---
layout: post
title: "passport-reverseproxy"
date: 2013-08-11 19:38
comments: true
categories: 
- node
- passport
---

Just released a new "API" provider for the NodeJS authentication middelware [Passport](http://passportjs.org).  The [ReverseProxy](https://npmjs.org/package/passport-reverseproxy) API provider enables NodeJS apps to authenticate users via HTTP Request Proxies injected by a reverse HTTP proxy server.  Reverse Proxy authentication is a technique seen in enterprise Single Sign On (SSO) where an authentication server sits in front of the web server/app server.  The proxy server authenticates users against some enterprise store (e.g., LDAP Directory).  

After successful authentication, the proxy will forward the original request to the target web server/app server.
Typically, the proxy will inject identifying information about the user (e.g., username, email address, display name) into custom HTTP request headers.  IBM's WebSeal application for SSO does exactly this.

Suprisingly, no one had written a passport strategy that uses custom HTTP headers yet.  So, I coded up an implementation,
inspired by Passport's `passport-http` strategy for HTTP basic authentication. 

Installing the provider is as simple as:

```bash
npm install express
npm install passport
npm install passport-reverseproxy
```

I've also included a sample Express app using the reverse proxy stategy in [passport-reverseproxy/examples/app.js](https://github.com/steve-jansen/passport-reverseproxy/tree/master/examples)

Overall, I think Passport is a great library - both easy to use and easy to implement custom strategies.

