---
layout: post
title: "Running Yeoman in a shell script"
date: 2013-08-27 15:44
comments: true
categories: 
- AngularJS
- Yeoman
---

I had a niche need to automate [yeoman](http://yeoman.io), and spent a few hours trying to overcome
yeoman's insistance on interactive prompts.  As far as I can tell the prompting library,
[Inquirer.js](https://github.com/SBoudrias/Inquirer.js), has no way to read a response file, or at least
be commanded to run in non-interactive mode.

Enter an old-school POSIX friend in core-utils... [yes](http://man7.org/linux/man-pages/man1/yes.1.html)

The `yes` utility continuously outputs 'Y' followed by a newline as long as another process is reading the stdout stream.
Very convenient for piping to another program.

Here's how I automated Yeoman to generate a new AngularJS app:

``` bash
# yeoman has a number of interactive prompts, and sadly doesn't support a batch mode
# so we use the coreutils `yes` utility to accept all the default answers in yeoman;
# yes is not part of msysgit, so skip this when running on windows without cygwin
which yes > /dev/null
if [ $? -eq 0 ]
then
	yes | yo angular
fi
```
