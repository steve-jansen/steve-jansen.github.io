---
published: false
layout: post
title: "Fundamentals of Windows Shell Scripting - Part 1 - Getting Started"
date: 2013-03-03 11:34
comments: true
categories: 
- windows
- batch
- shell
- scripting
---

## Series Parts
* [Overview](/blog/2013/03/01/fundamentals-of-windows-shell-scripting-overview/)
* [Part 1 - Getting Started with Windows Batch Scripting](/blog/2012/03/DD/title/)
* [Part 2 - Variables](/blog/2012/03/DD/title/)
* [Part 3 - Return Codes](/blog/2012/03/DD/title/)
* [Part 4 - The pseudofiles stdin, stdout, stderr, and more](/blog/2012/03/DD/title/)
* [Part 5 - If/Then Conditionals](/blog/2012/03/DD/title/)
* [Part 6 - Loops](/blog/2012/03/DD/title/)
* [Part 7 - Subroutines](/blog/2012/03/DD/title/)
* [Part 8 - Parsing Input](/blog/2012/03/DD/title/)
* [Part 9 - Logging](/blog/2012/03/DD/title/)
* [Part 10 - Advanced Tricks](/blog/2012/03/DD/title/)

## Getting Started with Windows Batch Scripting

Windows batch scripting is incredibly accessible - it works on just about any modern Windows machine.  You can create and modify batch scripts on just about any modern Windows machine.  The tools come out of the box: the Windows command prompt and a text editor like Notepad.exe.  

### Launching the Command Prompt
Windows gurus launch the command prompt using the keyboard shortcut `Windows Logo Key`+`R` (i.e., "Run") > Type `cmd.exe` then `Enter`.  This is way faster than navigating the Windows Start Menu to find the Command Prompt. 

### Editing Batch Files
The universal text editor for batch files is Notepad (`Windows Logo Key` + `R` > Type `notepad` then `Enter`).  Since batch files are just ASCII text, you can probably use just about any text editor or word processor.  Very few editors do anything special for Batch files like syntax highlighting or keyword support, so notepad is good enough fine and will likely be installed on just about every Windows system you encounter.

### Viewing Batch Files
I would stick with Notepad for viewing the contents of a batch file.  In Windows Explorer (aka, "My Computer"), you should be able to view a batch file in Notepad by right clicking the file and seleting `Edit` from the context menu. If you need to view the contents within a command prompt window itself, you can use a DOS command like `TYPE _myscript_.cmd` or `MORE _myscript_.cmd` or `EDIT _myscript_.cmd`

### Batch File Names and File Extensions
Assuming you are using Windows XP or newer, I recommend saving your batch files with the file extension `.cmd`.  Some seriously outdated Windows versions used `.bat`, though I recommend sticking with the more modern `.cmd` to [avoid some rare side effects with error checking a few built-in commands](http://waynes-world-it.blogspot.fr/2008/08/difference-between-bat-and-cmd.html).  

With the `.cmd` file extension, you can use just about filename you like.  I recommend avoiding spaces in filenames, as spaces only create headaches in shell scripting.  Pascal casing your filenames is an easy way to avoid spaces (e.g., `HelloWorld.cmd` instead of `Hello World.cmd`).  You can also use punctuation characters like `.` or `-` or `_` (e.g. `Hello.World.cmd`, `Hello-World.cmd`, `Hello_World.cmd`).

Another thing with names to consider is avoiding names that use the same name of any built-in commands, system binaries, or popular programs.  For example, I would avoid naming a script `ping.cmd` since there is a widely used system binary named `ping.exe`.  Things might get very confusing if you try to run `ping` and inadvertently call `ping.cmd` when you really wanted `ping.cmd`.  (Stay tuned for how this could happen.)  I might called the script `RemoteHeartbeat.cmd` or something similar to add some context to the script's name and also avoid any naming collisions with any other executable files.  Of course, there could be a very unique circumstance in which you want to modify the default behavior of `ping` in which this naming suggestion would not apply.

### Saving Batch Files in Windows
Notepad by default tries to save all files as plain jane text files.  To get Notepad to save a file with a `.cmd` extension, you will need to change the "Save as type" to "All Files (*.*)".  See the screenshot below for an example of saving a script named "HelloWorld.cmd" in Notepad.  

![Screenshot of saving a batch file in Notepad](images/2013-03-03-A.png)

> **SIDEBAR:** I've used a shortcut in this screenshot that you will learn more about later.  I've saved the file to my "user profile folder" by naming
> the file `%USERPROFILE%\HelloWorld.cmd`.   The `%USERPROFILE%` keyword is the Windows environmental variable for the full path
> to your user profile folder.  On newer Windows systems, your user profile folder will typically be `C:\Users\<your username>`.  This shortcut
> saves a little bit of time because a new command prompt will generally default the "working directory" to your user profile folder. This lets you run
> `HelloWorld.cmd` in a new command prompt without changing directories beforehand or needing to specify the path to the script.

### Running your Batch File
The easy way to run your batch file in Windows is to just double click the batch file in Windows Explorer (aka "My Computer").  Unfortunately, the command prompt will not give you much of a chance to see the output and any errors.  The command prompt window for the script will disappear as soon as the script exits.  (We will learn how to handle this problem in [Part 10 - Advanced Tricks](/blog/2012/03/DD/title/) ).

When editing a new script, you will likely need to run the batch file in an existing command window.  There are a few tricks to this
