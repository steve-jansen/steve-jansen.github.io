---
layout: post
title: "Tips for Vagrant on Windows"
date: 2014-03-13 05:55:12 -0400
comments: true
categories:
- windows
- vagrant
---

I learned some interesting things today about running Vagrant on a Windows machine.
Vagrant is an amazing tool for running a VM on your local dev box with a target platform (e.g., Linux)
provisioned by code (e.g., Chef/Puppet/shell scripts).

## Spaces in Paths
A hard lesson about Vagrant on Windows was Vagrant uses Ruby heavily, and Ruby on Windows
really, really doesn't like spaces in paths.

The Vagrant installer can't comply with the Windows Installer and Logo requirement to
default to `%ProgramFiles%` folder [due to Ruby's known issues with spaces in paths like C:\Program Files](https://github.com/mitchellh/vagrant/issues/1652).

I was able to work around this with a symlink:

``` console
IF NOT EXIST "%ProgramFiles%\Vagrant" MKDIR "%ProgramFiles%\Vagrant"
MKLINK /D "%SystemRoot%\vagrant" "%ProgramFiles%\Vagrant"
```

I then ran the [VirtualBox-4.3.8-92456-Win.exe](https://www.virtualbox.org/wiki/Downloads) installer using all defaults
except for the USB support and Python scripting.

**TIP:** do not install VirtualBox's USB drivers if you have an enterprise USB device blocker/filter

I then followed with installing [Vagrant_1.4.3.msi](http://www.vagrantup.com/download-archive/v1.4.3.html) to `C:\vagrant`.

**TIP:** [the Vagrant v1.5.0 installer is broken for Windows](https://github.com/mitchellh/vagrant/issues/3131); use [v1.4.3](http://www.vagrantup.com/download-archive/v1.4.3.html) until [v1.5.1](http://www.vagrantup.com/downloads.html) is released.


## VirtualBox in XP SP3 compatability mode

I needed to configure a few VirtualBox binaries to run in XP SP3 compatability mode for my Windows 7 SP1 Enterprise laptop.  YMMV.

``` console
REM run VirtualBox in XP SP3 mode
REG ADD "HKCU\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Layers" ^
        /v "%ProgramFiles%\Oracle\VirtualBox\VirtualBox.exe" ^
        /t REG_SZ  ^
        /d WINXPSP3
REG ADD "HKCU\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Layers" ^
        /v "%ProgramFiles%\Oracle\VirtualBox\VBoxSVC.exe" ^
        /t REG_SZ  ^
        /d WINXPSP3
REG ADD "HKCU\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Layers" ^
        /v "%ProgramFiles%\Oracle\VirtualBox\VBoxManage.exe" ^
        /t REG_SZ  ^
        /d WINXPSP3
```

## Spaces in your home folder path

If your Windows username (or `%USERPROFILE%` path) include spaces, you will need to set an environmental variable `%VAGRANT_HOME%` to a path that
does not use spaces.  This caused many non-obvious errors with `vagrant plugin install berkshelf` and `vagrant plugin install omnibus`.

A simple fix was setting `%VAGRANT_HOME%` to "C:\VagrantHome"

## Example running a simple 32-bit Ubuntu LTS box on 32-bit Windows 7 SP1

I don't really need the omnibus plugin here, but, this proves it can install a plugin that would
otherwise fail with spaces in the `%USERPROFILE%` path.

``` console
SETLOCAL
IF NOT EXIST C:\VagrantHome MKDIR C:\VagrantHome
PUSHD C:\VagrantHome
SET VAGRANT_HOME=C:\VagrantHome
PUSHD %TEMP%
MKDIR VagrantTest
CD VagrantTest
vagrant init hashicorp/precise32
vagrant box add hashicorp/precise32 https://vagrantcloud.com/hashicorp/precise32/version/1/provider/virtualbox.box
vagrant plugin install omnibus
vagrant up --provision
PAUSE
vagrant halt
vagrant destroy --force
CD ..
RMDIR /S /Q "%TEMP%\VagrantTest"
POPD
ENDLOCAL
```
