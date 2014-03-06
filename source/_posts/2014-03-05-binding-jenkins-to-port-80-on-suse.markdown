---
layout: post
title: "Binding Jenkins to port 80 on SUSE Linux"
date: 2014-03-05 18:09
comments: true
categories:
- jenkins
- suse
---

I've been helping an awesome colleague on DevOps for our Jenkins farm, which we use
for continuous integration and continuous deployment to our preproduction environments.

We are really trying to do it right:

* Use Puppet to provision the Jenkins master, Linux VM build slaves, Windows VM slaves,
  and even OS X bare metal slaves (for iOS builds)
* Automated backups of Jenkins config files to a private GitHub repo for disster recovery
* Patches the GitHub OAuth plugin to make sure you have the same collaborator permissions
  (read/write/admin) in a Jenkins job as you do the GitHub repo.
* Have a Jenkins staging environment to test upgrades to Jenkins and plugins to avoid surprises.
* Run Jenkins on the Long Term Support (LTS) release channel to avoid surprises.

I wish my shop used CentOS or Debian; sadly we are stuck on SUSE Enterprise.  SUSE is really
good at turning 5 minute tasks on CentOS or Debian into uber frustrating hour-long ordeals.

One of the glitches we faced was running the Jenkins web UI on port 80.  SUSE lacks the
authbind package for binding to port below port 1024 as a non-root user.  We wanted to run the
Jenkins deamon as a regular privilege user, so running as root was not an option.

We are currently smoke testing this LSB `/etc/init.d/jenkins.portforwarding` script, which is
just a wrapper around `iptables`.  So far, it seems to get the job done.


{% gist 9361161 %}

If all goes well, I will merge this logic into a pull request for the [Jenkins init.d script for OpenSuse](https://github.com/jenkinsci/jenkins/blob/master/opensuse/SOURCES/jenkins.init.in).

