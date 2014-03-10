---
published: false
layout: post
title: "Windows Batch Scripting: Advanced Tricks"
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
* [Part 8 - Parsing Input](/guides/windows-batch-scripting/part-8-parsing-input.html)
* [Part 9 - Logging](/guides/windows-batch-scripting/part-9-logging.html)
* Part 10 - Advanced Tricks

# Boilplate info

I like to include a header on all of scripts that documents the who/what/when/why/how.  You can use the `::` comment trick to make
this header info more readable:

    :: Name:     MyScript.cmd
    :: Purpose:  Configures the FooBar engine to run from a source control tree path
    :: Author:   stevejansen_github@icloud.com
    :: Revision: March 2013 - initial version
    ::           April 2013 - added support for FooBar v2 switches

    @ECHO OFF
    SETLOCAL ENABLEEXTENSIONS ENABLEDELAYEDEXPANSION

    :: variables
    SET me=%~n0


    :END
    ENDLOCAL
    ECHO ON
    @EXIT /B 0

# Conditional commands based on success/failure

The conditional operators `||` and `&&` provide a convenient shorthand method to run a 2nd command based on the succes or failure of a 1st command.

The `&&` syntax is the AND opeartor to invoke a 2nd command when the first command returns a zero (success) exit code.

    DIR myfile.txt >NUL 2>&1 && TYPE myfile.txt

The `||` syntax is an OR operator to invoke a 2nd command when the first command returns a non-zero (failure) exit code.

    DIR myfile.txt >NUL 2>&1 || CALL :WARNING file not found - myfile.txt

We can even combined the techniques.  Notice how we use the `()` grouping construct with `&&` to run 2 commands together should the 1st fail.

    DIR myfile.txt >NUL 2>&1 || (ECHO %me%: WARNING - file not found - myfile.txt >2 && EXIT /B 1)


# Getting the full path to the parent directory of the script

    :: variables
    PUSHD "%~dp0" >NUL && SET root=%CD% && POPD >NUL


# Making a script sleep for N seconds

You can use `PING.EXE` to fake a real *nix style `sleep` command.

    :: sleep for 2 seconds
    PING.EXE -N 2 127.0.0.1 > NUL


# Supporting "double-click" execution (aka invoking from Windows Explorer)

Test %CMDCMDLINE% is equal to %COMSPEC%  If so, enable a PAUSE at the end of the script.  Also remember to CD to a valid working directory if needed.

<span class="basic-alignment left">
[<< Part 9 - Logging](/guides/windows-batch-scripting/part-9-logging.html)
</span>
