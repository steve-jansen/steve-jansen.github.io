---
layout: post
title: "regexes for the serverspec 2 update"
date: 2014-10-03 18:24:25 -0400
comments: true
categories:
- chef
- devops
---

The Serverspec team just released v2 of their outstanding testing library
today, after a very long beta period.  The v2 release had a
[few breaking breaking changes](http://serverspec.org/changes-of-v2.html)
due to dropped rspec matchers that had been deprecated.

If your [test-kitchen](http://kitchen.ci/) tests recently broke today,
here's a few regexes I used with Sublime Text's regex find/replace
to rewrite the dropped matchers for the new matchers.

```
it\s*\{\s*(should|should_not)\s*return_(stdout|stderr)\s*\(?(\/.*\/)\)?\s*\}
its(:\2) { \1 match \3 }

it\s*\{\s*(should|should_not)\s*return_(stdout|stderr)\s*\(?(\".*\")\)?\s*\}
its(:\2) { \1 contain \3 }

it\s*\{\s*(should|should_not)\s*return_(stdout|stderr)\s*\(?('.*')\)?\s*\}
its(:\2) { \1 contain \3 }

it\s*\{\s*(should|should_not)\s*return_exit_status\s*(\d+)\s*\}
its(:exit_status) { \1 eq \2 }
```

Hopefully the kitchen busser project will one day add support for
Gemfile-style constraints on the test node, since busser always
[installs the latest version of a busser plugin gem today.](https://github.com/test-kitchen/test-kitchen/issues/242#issuecomment-28991870).
