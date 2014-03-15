---
layout: post
title: "How To verify administrative rights in a Windows batch script"
date: 2014-03-15 18:50:08 -0400
comments: true
categories:
- windows
- scripting
---

While working on automated provisioning of a Jenkins slave server on Windows, I needed
to verify that one of my batch scripts was running with administrative privileges.

Turns out this problem is easy to solve these days as long as you don't need to support
XP.  Thanks to [and31415 on SO](http://stackoverflow.com/a/21295806/1995977) for the
great post on using `fsutil`!

Here's a working example:

``` console
@ECHO OFF
SETLOCAL ENABLEEXTENSIONS

:: verify we have admin privileges
CALL :IsAdmin || (ECHO %~n0: ERROR - administrative privileges required && EXIT /B 1)

ECHO "Hello, Admin!"

:EXIT
EXIT /B

:: function to verify admin/UAC privileges
:: CREDIT: http://stackoverflow.com/a/21295806/1995977
:IsAdmin
IF NOT EXIST "%SYSTEMROOT%\system32\fsutil.exe" (
  ECHO %~n0: WARNING - fsutil command not found; cannot verify adminstrative rights
) ELSE (
  "%SYSTEMROOT%\system32\fsutil.exe" dirty query "%SystemDrive%" >NUL 2>&1
)
EXIT /B
```
Shameless plug - learn more tips and tricks for batch scripting in my [Guide to Windows Batch Scripting](/guides/windows-batch-scripting/)!
