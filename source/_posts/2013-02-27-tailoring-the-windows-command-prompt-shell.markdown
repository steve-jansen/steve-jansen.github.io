---
layout: post
title: "Tailoring the Windows Command Prompt Shell"
date: 2013-02-27 14:18
comments: true
categories: 
- windows
- tools
---

I spend a lot of time in the Windows Command Prompt.  Most developers I work with don't know some of the tricks up the Command Prompt's sleeve.  So allow me to share some of my favorite settings.

<!-- more -->

## Launching the Command Prompt
Windows gurus launch the command prompt using the keyboard shortcut `Windows Logo Key`+`R` (i.e., "Run") > Type `cmd.exe` then `Enter`.  This is way faster than navigating the Windows Start Menu to find the Command Prompt. 

## Preferences

Right click the Command Prompt title bar and select "Properties" from the context menu.  I modify the preferences highlighted in below screenshots to accomplish a few things:

* make the shell easier on our overtaxed eyes ;)
* quicker navigation of the command history (up/down arrows) by removing the noise of duplicate entries
* simplify copying text to copy any selected text (by the mouse) when you press enter
* simplify pasting text to paste on right click

![Screenshot of the Command Prompt Options tab](/images/2013-02-27-A.png)

![Screenshot of the Command Prompt Options tab](/images/2013-02-27-B.png)

![Screenshot of the Command Prompt Options tab](/images/2013-02-27-C.png)

![Screenshot of the Command Prompt Options tab](/images/2013-02-27-D.png)

## Tab and Filename Autocompletion
The `Tab` key should auto complete the names of files and folders in the current working directory.  Each press of `Tab` should cycle to the next name.  The autocompletion is pretty smart - it will filter the choices based on what you have already typed.   For example, typing `D` then `Tab` will autocomplete the first file or folder that starts with the letter "D".  Tabbing again will display the next file or folder that start with D.  Tabbing will cycle through a ring of possible matches.  

For example, tabbing through the letter "D" in your user profile directory on Windows 7 might autocomplete "Desktop", "Documents", "Downloads", and then back to "Desktop".  Autocomplete will even quote paths with spaces, which is a nice trick.  I often see experienced developers stuggle to find a filepath, repeatedly doing `DIR` and `CD` commands to just run a single batch script.

One small shortcoming is autocomlete cannot expand environmental variables like `%ProgramFiles%` or `%UserProfile%`.  Although you can use `Windows logo key` + `R` Run dialog to autocomplete environmental variables.

Autocompletion saves you a bunch of typing with just a key or two to filter your choices, tabbing until you find the folder you want,  pressing `\` to start tabbing through the subfolder, and repeat.  And it makes you look more competent when pair programming.

## Command History
The `DOSKEY` utility is similar to the `history` utility on Unix/Linux shells.  Most developers know you can press the up arrow or down arrow to cycle through the history of issued commands.  

Most developers (and even sysadmins) aren't aware of the advanced keyboard shortcuts wired to `DOSKEY`:

* UP and DOWN ARROWS recall commands
* `ESC` clears command line
* `F7` displays command history
* `ALT`+`F7` clears command history
* `F8` searches command history
* `F9` selects a command by number

Example of pressing `ALT`+`F7` to view the command history:
![Screenshot of the visual command history](/images/2013-02-27-E.png)

Example of pressing `F9` to recall a command by history number:
![Screenshot of the visual command history](/images/2013-02-27-F.png)

Example of pressing `F8` to cycle through history entries matching the text "D" already typed into the comamnd line buffer:
![Screenshot of the visual command history](/images/2013-02-27-G.png)

