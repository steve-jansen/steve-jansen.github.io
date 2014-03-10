---
published: false
layout: post
title: "Windows Batch Scripting: Loops"
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
* [Part 5 - If/Then Conditionals](/guides/windows-batch-scripting/part-5-if-then-conditionals.html)
* Part 6 - Loops
* [Part 7 - Functions](/guides/windows-batch-scripting/part-7-functions.html)
* [Part 8 - Parsing Input](/guides/windows-batch-scripting/part-8-parsing-input.html)
* [Part 9 - Logging](/guides/windows-batch-scripting/part-9-logging.html)
* [Part 10 - Advanced Tricks](/guides/windows-batch-scripting/part-10-advanced-tricks.html)

Looping through items in a collection is a frequent task for scripts.  It could be looping through files in a directory, or reading a text file one
line at a time.

## Old School with GOTO

The old-school way of looping on early versions of DOS was to use labels and GOTO statements.  This isn't used much anymore, though it's useful for
looping through command line arguments.

    :args
    SET arg=%~1
    ECHO %arg%
    SHIFT
    GOTO :args

## New School with FOR

The modern way to loop through files or text uses the `FOR` command.  In my opinion, `FOR` is the single most powerful command in DOS, and one of
the least used.

**GOTCHA:** The `FOR` command uses a special variable syntax of `%` followed by a single letter, like `%I`.  This syntax is slightly different when
`FOR` is used in a batch file, as it needs an extra percent symbol, or `%%I`.  This is a very common source of errors when writing scripts.  Should
your for loop exit with invalid syntax, be sure to check that you have the `%%` style variables.

## Looping Through Files
    FOR %I IN (%USERPROFILE%\*) DO @ECHO %I

## Looping Through Directories
    FOR /D %I IN (%USERPROFILE%\*) DO @ECHO %I

Recursively loop through files in all subfolders of the %TEMP% folder
    FOR /R "%TEMP%" %I IN (*) DO @ECHO %I

Recursively loop through all subfolders in the %TEMP% folder
    FOR /R "%TEMP%" /D %I IN (*) DO @ECHO %I

## Variable Substitution for Filenames



<span class="basic-alignment left">
[<< Part 5 - If/Then Conditionals](/guides/windows-batch-scripting/part-5-if-then-conditionals.html)
</span>
<span class="basic-alignment right">
[Part 7 - Functions >>](/guides/windows-batch-scripting/part-7-functions.html)
</span>
