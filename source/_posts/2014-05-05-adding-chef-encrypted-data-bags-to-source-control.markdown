---
layout: post
title: "Adding Chef encrypted data bags to source control"
date: 2014-05-05 20:54:25 +0100
comments: true
categories: 
- chef
- devops
---

I've been using Chef for a bit now and generally a huge fan of the new
[Chef workflow](http://www.getchef.com/downloads/chef-dk/).

We are working hard to attain true continuous delivery and test driven development with Chef.
The devil is in the details now.

One small wrinkle in our effort has been marrying [encrypted data_bags](http://docs.opscode.com/chef/essentials_data_bags.html#encrypt-a-data-bag-item) with our chef-repo in GitHub.  

I don't want to type the optional argument `--secret-file ~/.chef/encrypted_data_bag_secret` everytime I interact with a data bag. So, I added this option to my `~/.chef/knife.rb` file.

``` ruby
knife[:secret_file]  = "#{current_dir}/encrypted_data_bag_secret"
```

However, this precludes me from easily exporting the edited file to disk.  The export will always be my secret plaintext,
not the encrypted ciphertext.   Not exactly what you want to commit to GitHub.

``` ruby
knife data_bag create users jenkins
# DON'T COMMIT THIS... the exported file will be unencrypted
knife data_bag users jenkins --format=json > data_bags/users/jenkins.json
```

So, I decided to create a bash alias to temporarily disable the knife.rb setting and export the data bag to a file:

My `~/.bash_profile` file contains this alias:

```
function knife-ciphertext () {
   sed -e "s/knife\[\:secret_file\]/\#knife\[\:secret_file\]/"  -i .bak  ~/.chef/knife.rb
   knife $@ --format=json
   mv  ~/.chef/knife.rb.bak  ~/.chef/knife.rb
}
alias knife-ciphertext=knife-ciphertext
```

This bash function comments out the secret file option in knife.rb using sed's in-place editing.

Now I can commit the data bag in its encrypted format:

```
knife-ciphertext data_bag show users jenkins > data_bags/users/jenkins.json
git add data_bags/users/jenkins.json
git commit -m 'adding the latest jenkins data bag'
```

Happing cooking!
