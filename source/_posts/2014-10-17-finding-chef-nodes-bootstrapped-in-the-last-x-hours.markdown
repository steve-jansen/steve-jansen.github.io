---
layout: post
title: "Finding Chef nodes bootstrapped in the last X hours"
date: 2014-10-17 18:47:44 -0400
comments: true
categories:
- chef
- devops
---

I needed to write a script to garbage collect old nodes in Chef related to
auto-scaling groups.

I decided to search for nodes bootstrapped in the last X hours.

I experimented with ways to find nodes that have been up for less than X hours.
In this example, I search for nodes that have been up for 8 hours or less.
Of course, this assumes you never restart your nodes:

```
knife exec -E 'search(:node, "uptime_seconds:[0 TO #{ 8 * 60 * 60 }]") { |n| puts n.name }'
```
I also tried finding nodes that converged in the last 8 hours (which would have
to be combined with some other filter of course):

```
knife exec -E 'b = Time.now.to_i; a = (b - (8*60*60)).to_i; search(:node, "ohai_time:[#{a} TO #{b}]") { |n| puts n.name }'
```

Overall, I think the easiest option is to just set a node attribute like
'bootstrap_date' at bootstrap (or set it if it's nil).  This would be a clearcut
way to find out how old a node truly is.

One of my colleagues pointed out that [Chef Metal](https://github.com/opscode/chef-metal)
sets a very handy `node['metal']['location']['allocated_at']` attribute that gets
the job done if you are spinning up new nodes with metal.
