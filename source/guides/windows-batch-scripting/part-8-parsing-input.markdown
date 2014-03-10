---
published: false
layout: post
title: "Windows Batch Scripting: Parsing Input"
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
* [Part 6 - Loops](/guides/windows-batch-scripting/part-6-loops.html)
* [Part 7 - Functions](/guides/windows-batch-scripting/part-7-functions.html)
* Part 8 - Parsing Input
* [Part 9 - Logging](/guides/windows-batch-scripting/part-9-logging.html)
* [Part 10 - Advanced Tricks](/guides/windows-batch-scripting/part-10-advanced-tricks.html)

Robust parsing of command line input separates a good script from a great script.  I'll share some tips on how I parse input.

# The Easy Way to read Command Line Arguments

By far the easiest way to parse command line arguments is to read required arguments by ordinal position.

Here we get the full path to a local file passed as the first argument.  We write an error message to stderr if the file does not exist and exit
our script:

    SET filepath=%~f1

    IF NOT EXIST "%filepath%" (
        ECHO %~n0: file not found - %filepath% >&2
        EXIT /B 1
    )


# Optional parameters

I assign a default value for parameters as such


    SET filepath=%dp0\default.txt

    :: the first parameter is an optional filepath
    IF EXIST "%~f1" SET filepath=%~f1

# Switches

# Named Parameters

# Variable Number of Arguments



# Reading user input

    :confirm
    SET /P "Continue [y/n]>" %confirm%
    FINDSTR /I "^(y|n|yes|no)$" > NUL || GOTO: confirm



<span class="basic-alignment left">
[<< Part 7 - Functions](/guides/windows-batch-scripting/part-7-functions.html)
</span>
<span class="basic-alignment right">
[Part 9 - Logging >>](/guides/windows-batch-scripting/part-9-logging.html)
</span>
