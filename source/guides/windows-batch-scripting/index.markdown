---
published: false
layout: post
title: "Guide to Windows Batch Scripting"
date: 2013-03-01 10:57
comments: true
categories:
- windows
- batch
- shell
- scripting
---

I *love* shell scripting - it's the duct tape of programming to me.  Low cost, high benefit.  And it feels like art, where one can learn to do increasinlgy complex tasks with greater simplicity.

Sadly, I feel like it's a developer skill on the decline.  Maybe new developers feel it's "not real programming".  Perhaps the growing dominance of Java as the *lingua franca* of academic comp sci courses has made shell scripting less relevant in university settings.

True, shell scripting feel a bit “vocational”, maybe even a bit unsexy compared to Python/Ruby/LISP/blah/blah/blah.  Nevertheless, it's a skill that becomes invaluable as you gain seniority and start doing more [DevOps](http://en.wikipedia.org/wiki/DevOps) in you day job, or if you want to do some high-speed, low drag stuff to tailor your development environment like [this](https://github.com/blog/1345-introducing-boxen).

## Why Windows?
This series will share some of the tips and tricks I've picked up through the years of working with Windows professionally.  I'll be the first to admit the Unix shells of the world are far superior to the Windows command prompt (or even Windows PowerShell).  Windows is a fact of life for most professionals writing code for coporate customers; this series aims to make life with Windows a little easier.

## Why DOS-style Batch Files?
This series will share some conventions I picked up along the way for scripting in Windows via command prompt batch files.  The Windows PowerShell is definitely sweet, but, I still like batch files for their portability and low friction.  The Windows command line is very stable - no worrying about the PowerShell interpreter path, which version of PowerShell the server is running, etc.

## Series Parts
* [Part 1 - Getting Started](/guides/windows-batch-scripting/part-1-getting-started.html)
* [Part 2 - Variables](/guides/windows-batch-scripting/part-2-variables.html)
* [Part 3 - Return Codes](/guides/windows-batch-scripting/part-3-return-codes.html)
* [Part 4 - stdin, stdout, stderr](/guides/windows-batch-scripting/part-4-stdin-stdout-stderr.html)
* [Part 5 - If/Then Conditionals](/guides/windows-batch-scripting/part-5-if-then-conditionals.html)
* [Part 6 - Loops](/guides/windows-batch-scripting/part-6-loops.html)
* [Part 7 - Functions](/guides/windows-batch-scripting/part-7-functions.html)
* [Part 8 - Parsing Input](/guides/windows-batch-scripting/part-8-parsing-input.html)
* [Part 9 - Logging](/guides/windows-batch-scripting/part-9-logging.html)
* [Part 10 - Advanced Tricks](/guides/windows-batch-scripting/part-10-advanced-tricks.html)


