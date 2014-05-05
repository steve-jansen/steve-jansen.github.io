---
layout: post
title: "Including another berksfile in your berksfile"
date: 2014-05-06 18:11:57 +0100
comments: true
categories: 
- chef 
- Berkshelf
- devops
---

As part of my cooking with [Chef's new workflow](http://www.getchef.com/downloads/chef-dk/),
I wanted Berkshelf to dynamically import the secondary dependencies of my site-cookbook's dependencies.  

Thanks [Vasily Mikhayliche's Coderwall post](https://coderwall.com/p/j72egw) and [Seth Vargo's post on Berks magic](https://sethvargo.com/berksfile-magic/), I was able to hack something
that worked for me with [Berkshelf v2.0](http://berkshelf.com/v2.0/). (We don't have time to migrate to Berks 3.0 for another couple of weeks, and this feature doesn't seem to be part of Berks 3.0).

``` ruby
# vi:ft=ruby:
site :opscode

# Extension method to import secondary dependencies in a referenced site-cookbook
# using the constraints in the site-cookbook's Berkshelf file, rather than just
# the name of the dependencies in the site-cookbook's metadata.rb file
#
# credit: https://sethvargo.com/berksfile-magic/
#         https://coderwall.com/p/j72egw
def site_cookbook(path)
  berksfile = "../#{path}/Berksfile"

  if File.exists?(berksfile)
    contents = File.read(berksfile)

    # comment out lines like `site :opscode`, which cannot be imported multiple times
    contents = contents.gsub(/(^\s*site\s)/, '#\1')

    # comment out lines like `metadata`, which cannot be imported multiple times
    contents = contents.gsub(/(^\s*metadata\s)/, '#\1')
    
    instance_eval(contents) 
  end
end

cookbook 'nginx', '~> 2.4.4'
site_cookbook 'my-site-cookbook'

```

Happy cooking!
