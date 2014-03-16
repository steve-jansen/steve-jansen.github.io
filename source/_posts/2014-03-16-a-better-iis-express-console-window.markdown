---
layout: post
title: "A better IIS Express console window"
date: 2014-03-16 18:18:34 -0400
comments: true
categories:
- windows
- iis
- .net
---

IIS Express is the _de facto_  server to use for local development of ASP.NET MVC and Web Api apps.  It's just like it's big brother IIS
minus a few features rarely used for local development.
Unlike it's big brother, IIS Express runs on demand as a regular console app under the security context of
your current login.  This makes it much easier to start and stop debugging sessions.

Being a console app is great - you can see `System.Diagnostics.Debug.Print` and `System.Diagnostics.Trace.Write`
output right in the console alongside IIS' usual log statements for HTTP requests.

A really useful trick is to create a Windows Explorer shortcut to iisexpress.exe, and open that shortcut iisexpress.exe.lnk file instead of directly
opening iisexpress.exe. There are two benefits to this:

1. iisexpress.exe gets a dedicated icon on the Windows taskbar.  In the screenshot below, I can `WinKey + 5` to quickly switch to my IIS Express
console output.  (`WinKey + N` focuses/opens the Nth item on the taskbar; repeat as needed if you have multiple windows grouped for that taskbar icon).

2. I can customize the command prompt preferences for just iisexpress.exe.  In the screenshot below, I'm using a smaller font in purple color, with the
window stretched the entire 1600 pixel width of my display. This helps greatly with the readability of long lines of text in the console output.


![Screenshot of the iisexpress.exe open in a custom window](/images/2014-03-16-A.png)

Here's a closer look at the console ouptut:
![Screenshot of the iisexpress.exe open in a custom window](/images/2014-03-16-H.png)

Here are screenshots of the Explorer settings I used for `C:\Program Files\IIS Express\iisexpress.exe.lnk`:

![Screenshot of the iisexpress.exe.lnk settings](/images/2014-03-16-B.png)
![Screenshot of the iisexpress.exe.lnk settings](/images/2014-03-16-C.png)
![Screenshot of the iisexpress.exe.lnk settings](/images/2014-03-16-D.png)
![Screenshot of the iisexpress.exe.lnk settings](/images/2014-03-16-E.png)
![Screenshot of the iisexpress.exe.lnk settings](/images/2014-03-16-F.png)
![Screenshot of the iisexpress.exe.lnk settings](/images/2014-03-16-G.png)
