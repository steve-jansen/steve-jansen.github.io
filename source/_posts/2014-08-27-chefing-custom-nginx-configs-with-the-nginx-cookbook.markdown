---
layout: post
title: "Chef'ing custom nginx configs with the nginx cookbook"
date: 2014-08-27 19:41:10 -0400
comments: true
categories:
- chef
- devops
---

The [nginx](http://supermarket.getchef.com/cookbooks/nginx) cookbook has been
super helpful Chef'ing some web apps recently.  One thing I struggled to
understand was how to use my own custom conf, like `/etc/nginx/nginx.conf`, that
is optimized for how I use nginx.

One solution I tried, which is probably a Chef anti-pattern, is to only include
the nginx cookbook on the initial converge:

## The Wrong Way

``` ruby
# the nginx community cookbook will relentlessly revert conf files,
# so avoid running it unless nginx isn't installed,
# or we explicitly reset/delete the node attribute
include_recipe 'nginx' unless node['nginx']['installed']
node.set['nginx']['installed'] = true

# our custom nginx.conf
template '/etc/nginx/nginx.conf' do
   source 'nginx.conf.erb'
   owner 'root'
   group 'root'
   mode  '0644'
   notifies :reload, "service[nginx]", :delayed
end
```

I knew this was wrong when I wrote it.   Chef is all about idempotency.
But, I couldn't figure out a way to keep the nginx cookbook from reverting my
custom conf during subsequent converges, only to have my `template` restore my
custom conf a few seconds later.

## The Better Way

The OpsCode blog [Doing Wrapper Cookbooks Right](http://www.getchef.com/blog/2013/12/03/doing-wrapper-cookbooks-right/) shows the right way, and really opened my eyes on the power of
Chef's two phase model (compile, then converge).

``` ruby
include_recipe 'nginx'

# use our custom nginx.conf, rather than the one that ships in the nginx cookbook
# this avoids the nginx and my-app cookbooks from fighting for control of
# the same target file
resources('template[nginx.conf]').cookbook 'my-app'
```
