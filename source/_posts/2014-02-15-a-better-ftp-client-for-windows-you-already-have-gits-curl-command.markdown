---
layout: post
title: "A better FTP client for Windows you already have: Git bash's curl command"
date: 2014-02-15 17:24
comments: true
categories:
- windows
- git
---

My shop has a couple of internal FTP servers to mirror commonly used installers for .Net devs.
Installers for apps like Visual Studio can be huge, so GitHub isn't the best place for this, and
it would also smoke most of our Dropbox quotas.  So an FTP server seems like the 3rd best option.

We are a geographically distributed team, with a VPN to access internal servers.  Even with a reliable
VPN session over ISP fiber connection, I've experience lots of realiability problems downloading large files
with the native Windows Explorer / Internet Explorer FTP.

The Windows ftp command line client can be a pain to work with.  Fortunately, the Git bash emulator for Windows
(msysgit) includes a MinGW port of the awesome `curl` utility.  The `curl` utility has all kinds of awesome features
for downloading large files.

Here's a few options I found really useful:

``` bash
curl -C - -v -O "ftp://ftp.example.com/path/to/file.zip"
```

* `-C -` option tells bash to automatically continue an interrupted download, if the server supports this feature.
* `-v` prints verbose stats, including an dynamic progress info
* `-O` automatically saves the file using the remote file name to the current working directory


I crafted this gist to enable downloading a large number of binaries related to .Net development from our FTP server.

{% gist 9378219 %}

Be warned, this hack spawns a new command prompt window for each download, so it can get a bit crazy.  This seemed like
the best worst way to download in parallel while also making sense of each download's status.
