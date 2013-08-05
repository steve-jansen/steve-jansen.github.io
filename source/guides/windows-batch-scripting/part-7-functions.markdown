---
published: false
layout: post
title: "Windows Batch Scripting: Functions"
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
* Part 7 - Functions
* [Part 8 - Parsing Input](/guides/windows-batch-scripting/part-8-parsing-input.html)
* [Part 9 - Logging](/guides/windows-batch-scripting/part-9-logging.html)
* [Part 10 - Advanced Tricks](/guides/windows-batch-scripting/part-advanced-tricks.html)


Functions are de facto way to reuse code in just about any procedural coding language.  While DOS lacks a bona fide function keyword, you can
fake it till you make it thanks to labels and the `CALL` keyword.

There are a few gotchas to pay attention to:

1.  Your quasi functions need to be defined as labels at the bottom of your script.
2.  The main logic of your script must have a `EXIT /B [errorcode]` statement.  This keeps your main logic from falling through into your functions.

# Defining a function

In this example, we'll implement a poor man's version of the *nix tee utility to write a message to both a file and the stdout stream.  We'll use a variable global to the entire script, `%log%` in the function.

    @ECHO OFF
    SETLOCAL

    :: script global variables
    SET me=%~n0
    SET log=%TEMP%\%me%.txt

    :: The "main" logic of the script
    IF EXIST "%log%" DELETE /Q %log% >NUL

    :: do something cool, then log it
    CALL :tee "%me%: Hello, world!"

    :: force execution to quit at the end of the "main" logic
    EXIT /B %ERRORLEVEL%

    :: a function to write to a log file and write to stdout
    :tee
    ECHO %* >> "%log%"
    ECHO %*
    EXIT /B 0

# Calling a function
    
We use the `CALL` keyword to invoke the quasi function labelled `:tee`.  We can pass command line arguments just like we're calling another batch file.

We have to remember to `EXIT /B` keyword at the end our function.  Sadly, there is no way to return anything other than an exit code.


# Return values

The return value of `CALL` will always be the exit code of the function.  Like any other invokation of an executable, the caller reads `%ERRORLEVEL%`
to get the exit code.

You have to get creative to pass anything other than integer return codes.  The function can `ECHO` to stdout, letting the caller decide to handle the
output by pipeling the output as the input to another executable, redirecting to a file, or parsing via the `FOR` command.

The caller could also pass data by modifying a global variable, however, I try to avoid this approach.


<span class="basic-alignment left">
[<< Part 6 - Loops](/guides/windows-batch-scripting/part-6-loops.html)
</span>
<span class="basic-alignment right">
[Part 8 - Parsing Input >>](/guides/windows-batch-scripting/part-8-parsing-input.html)
</span>

