---
layout: post
title: "Parsing Jenkins secrets in a shell script"
date: 2014-12-16 14:39:54 -0500
comments: true
categories: 
- devops
- jenkins
---

The [Jenkins credentials-binding plugin](https://wiki.jenkins-ci.org/display/JENKINS/Credentials+Binding+Plugin)
provides a convenient way to securely store secrets like usernames/passwords in
Jenkins.  You can even inject these secrets into build steps as environmental
variables in a job like this:

![screenshot of a Jenkins job using the credentials-binding plugin](/images/2014-12-16.png)

For a username/password pair, the plugin will inject the pair as a single value
joined by `:`.  You can split the credentials into their respective parts
using [bash string manipulation operators like `%` and `#`](http://spin.atomicobject.com/2014/02/16/bash-string-maniuplation/).

Assuming you configured the job to inject a variable named `CREDENTIALS`, you can do:

``` bash [parsing Jenkins secret credentials with bash]
USERNAME=${CREDENTIALS%:*}
PASSWORD=${CREDENTIALS#*:}

# proof of concept - don't echo this in real life :)
echo USERNAME=$USERNAME
echo USERNAME=$PASSWORD
```
