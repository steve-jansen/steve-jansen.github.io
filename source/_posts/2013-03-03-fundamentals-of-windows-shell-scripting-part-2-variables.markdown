---
published: false
layout: post
title: "Fundamentals of Windows Shell Scripting - Part 2 - Variables"
date: 2013-03-03 15:19
comments: true
categories: 
---

Variables 
The common type of variables in DOS batch files are surrounded by the % character, like the well know environmental variable %PATH% 
The list of environmental variables can be retrieved using the SET command with no arguments 
        TODO Show example of SET 
Variables with the % notation are persistent throughout your command prompt session by default.  You can declare that all future variable assignments in a batch file will be local to the scope of the batch file using the SETLOCAL command.  Any changes you make to the command environment will revert when you batch file exits. 
        TODO show example of SETLOCAL with PATH 
There are a few other types of variables with slightly different syntax.  You can read the command line arguments using a single % character followed by the ordinal position of the argument.  The numbering of the arguments starts at zero, however, the zero ordinal argument is the name of the batch file itself.  So the variable %0 in a script “C:\demo\test.cmd” will usually be something like test.cmd or C:\demo\test.cmd   
If you invoke the script “test.cmd foo bar” The variable %0 will be test.cmd, %1 will be foo, %2 will be bar, while %3 and higher will be an empty string.  The variables will be 
Who am I? 
The preferred file extension for Windows batch files is “.cmd”.  You will see “.bat” as well, but, this is for backwards compatibility with 16-bit scripts in the command prompt.   Stick to “.cmd” files. 
Where do I live? 
I like to add a little polish to my scripts to ensure the scripts run when executed from any working directory.   It bugs me when a script assumes that the working directory must be the same directory as the parent folder of the script. 
Take for example this snippet from a batch file that I wrote to run the various MSI installers for the dependencies of an ASP.NET MVC4 application. 
MSIEXEC.EXE /I AspNetMVC4.msi /QUIET /NORESTART ADDLOCAL=ALL ALLUSERS=1 
This code works fine if the current working directory is the same place as the parent folder of the AspNetMVC4.msi file.  But what happens if I invoke the script from a different working directory than where AspNetMVC4.msi lives? As is, the line fails (quietly).   
This is particularly a problem when a user launches the script from Windows Explorer, say by double clicking the script file.  By default, the command processor will launch the script with a working directory of %HOMEDRIVE%\%HOMEPATH%, which might be something like C:\Users\Me 
If AspNetMVC4.msi doesn’t live in C:\Users\Me, our script now breaks.  So, how do we fix it? 
Fortunately, there are clever hacks to fix this.  