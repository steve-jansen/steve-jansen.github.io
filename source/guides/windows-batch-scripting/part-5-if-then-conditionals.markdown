---
published: false
layout: post
title: "Windows Batch Scripting: If/Then Conditionals"
date: 2013-03-01 10:57
comments: true
categories:
- windows
- batch
- shell
- scripting
---

* [Overview](/guides/windows-batch-scripting/index.html)
* [Part 1 - Getting Started](/guides/windows-batch-scripting/part-1-getting-started.html)
* [Part 2 - Variables](/guides/windows-batch-scripting/part-2-variables.html)
* [Part 3 - Return Codes](/guides/windows-batch-scripting/part-3-return-codes.html)
* [Part 4 - stdin, stdout, stderr](/guides/windows-batch-scripting/part-4-stdin-stdout-stderr.html)
* Part 5 - If/Then Conditionals
* [Part 6 - Loops](/guides/windows-batch-scripting/part-6-loops.html)
* [Part 7 - Functions](/guides/windows-batch-scripting/part-7-functions.html)
* [Part 8 - Parsing Input](/guides/windows-batch-scripting/part-8-parsing-input.html)
* [Part 9 - Logging](/guides/windows-batch-scripting/part-9-logging.html)
* [Part 10 - Advanced Tricks](/guides/windows-batch-scripting/part-10-advanced-tricks.html)

Computers are all about 1's and 0's, right?  So, we need a way to handle when some condition is 1, or else do something different
when it's 0.

The good news is DOS has pretty decent support for if/then/else conditions.

## Checking that a File or Folder Exists

    IF EXIST "temp.txt" ECHO found

Or the converse:

    IF NOT EXIST "temp.txt" ECHO not found

Both the true condition and the false condition:

    IF EXIST "temp.txt" (
        ECHO found
    ) ELSE (
        ECHO not found
    )

NOTE: It's a good idea to always quote both operands (sides) of any IF check.  This avoids nasty bugs when a variable doesn't exist, which causes
the the operand to effectively disappear and cause a syntax error.

## Checking If A Variable Is Not Set

    IF "%var%"=="" (SET var=default value)

Or
    IF NOT DEFINED var (SET var=default value)

## Checking If a Variable Matches a Text String

    SET var=Hello, World!

    IF "%var%"=="Hello, World!" (
        ECHO found
    )

Or with a case insensitive comparison

    IF /I "%var%"=="hello, world!" (
        ECHO found
    )

## Artimetic Comparisons

    SET /A var=1

    IF /I "%var%" EQU "1" ECHO equality with 1

    IF /I "%var%" NEQ "0" ECHO inequality with 0

    IF /I "%var%" GEQ "1" ECHO greater than or equal to 1

    IF /I "%var%" LEQ "1" ECHO less than or equal to 1

## Checking a Return Code

    IF /I "%ERRORLEVEL%" NEQ "0" (
        ECHO execution failed
    )

<span class="basic-alignment left">
[<< Part 4 - stdin, stdout, stderr](/guides/windows-batch-scripting/part-4-stdin-stdout-stderr.html)
</span>
<span class="basic-alignment right">
[Part 6 - Loops >>](/guides/windows-batch-scripting/part-6-loops.html)
</span>
