---
layout: post
title: "Configuring Vagrant to dynamically match guest and host CPU architectures"
date: 2014-03-14 20:06:26 -0400
comments: true
categories:
- vagrant
- windows
- ruby
---

Today a work colleague put together a nice Vagrantfile to run a Linux dev environment on our laptops.  [Vagrant](http://www.vagrantup.com)
is sweet for DevOps.  The Vagrant file worked great on his Macbook Pro.
But it was no dice running on my Windows box - the VM was a 64-bit Linux VM (why wouldn't a server be 32-bit?) and I'm on a
32-bit laptop (don't ask why my corporate IT still issues 32-bit Windows images on 64-bit hardware!).

To my surprise, VirtualBox can actually a 64-bit guest VM on a 32-bit host OS:

> If you want to use 64-bit guest support on a 32-bit host operating system, you must also select a 64-bit operating system for the particular VM. Since supporting 64 bits on 32-bit hosts incurs additional overhead, VirtualBox only enables this support upon explicit request.

> Source: http://www.virtualbox.org/manual/ch03.html

However, I learned Vagrant cloud boxes [may forget to explicity declare they want VirtualBox to enable  64-on-32 support](https://github.com/mitchellh/vagrant/issues/932).  While changing the box "Operating System Type" from "Ubuntu" to "Ubuntu (64 bit)" would be
an easy fix, I decided to see if Vagrant could dynamically choose the right guest CPU architecture based on the host OS' CPU architecture.
Our app would run as either 32 or 64, so it made sense to run 32 on 32 and 64 on 64, right?


Turns out it is quite easy.  The power of ruby as the config language for Vagrant really shines here:

Here the relevant changes to our Vagrantfile to get Vagrant to run a 64-bit Linux guest on 64-bit hosts,
and a 32-bit Linux guest on 32-bit hosts:

``` ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/precise64"
  config.vm.box_url = "https://vagrantcloud.com/hashicorp/precise64/current/provider/virtualbox.box"

  # support 32 windows hosts :(
  if ENV["PROCESSOR_ARCHITECTURE"] == "x86"
    puts "falling back to 32-bit guest architecture"
    config.vm.box = "hashicorp/precise32"
    config.vm.box_url = "https://vagrantcloud.com/hashicorp/precise32/current/provider/virtualbox.box"
  end

  # ... lots more vagrant plugin and chef goodness ...

end
```

