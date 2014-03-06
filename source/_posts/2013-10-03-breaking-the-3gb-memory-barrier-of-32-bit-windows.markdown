---
layout: post
title: "Breaking the 3GB memory barrier of 32-bit Windows"
date: 2013-10-03 21:22:02 -0500
comments: true
categories: 
- windows
---

My corporate laptop has 6 GB of RAM installed, but, only sees 3 GB of logical RAM. Why?  My corporate IT department
images laptops with the 32-bit flavor of  Windows 7. 

As you can see in this screenshot from my Control Panel's System information applet, installing more memory hits a glass ceiling with Windows at ~3GB. 

![System information screenshot showing 3GB of RAM](/images/2013-10-03-A.gif)

My laptop has 6 GB of physical RAM installed, yet my user applications have access to less than half of the physical memory!

## Hacking a Solution: "Physical Virtual Memory"

Fortunately, there is a solution to this problem. It's a hack and it uses a reasonably priced piece of 3rd party commercial software. 

The solution combines a feature of Windows known as Physical Address Extensions (PAE) in tandem with a RAMDISK as the storage "disk" for the virtual memory paging file. The result is a total hack - we're using a page file to expose the address space of physical memory. It's "physical virtual" memory. An oxymoron if I ever heard one! 

A commercial software package called [Primo Ramdisk Standard](http://www.romexsoftware.com/en-us/primo-ramdisk/) by Romex Software is needed to create the Ramdisk. It's $30/seat. 

This is the only Ramdisk driver I could find that: 

1. Supports Windows 7
2. Supports PAE
3. Supports the Intel/AMD physical memory remapping ("Invisible Memory") chipset feature ([read more](http://www.polywell.com/us/support/faq/4gb_rev1.pdf))
4. Not flagged as a removable storage device by our corporate data loss prevention nanny software

## Performance

Indeed, the performance of this hack to use "physical virtual memory" will be less than just using a 64 bit O/S with it's address space of 2^64 bytes. Nevertheless, paging to a RAMDISK will always beat paging to a magnetic hard drive, and will probably beat paging to a SSD disk as well. 

I speculate there are a number of very good reasons why corporate IT would deploy 32-bit over 64-bit - availability of 64-bit client software for VPNs, anti-malware, remote backup agents, remote support agents, encryption policy engines; the difficulty in recreating and testing a new image from scratch; the density of older 32-bit laptops still in use. 

## Known Issues

**Caveat Emptor:**  You must disable hibernation mode.  Hibernating sporadically crashes upon shutdown or startup when using this hack.  The good news is you will not miss much.  My laptop clocked faster times with a normal shutdown/startup cycle compared to the time required to enter and exit hibernation.  The disk IO was just too slow to copy 6 GB of RAM contents to into and out of the C:\hiberfil.sys hibernation file. 

## Testing

This setup was tested successfully for over one year on a Lenovo ThinkPad T410 with 6 GB of RAM (2 GB +4 GB DIMMS) as well as one year on a Lenovo T420s with 8 GB of RAM.  Please test your setup.  Should your machine fail to restart after following below steps, you should boot into Windows Safe Mode and disable/uninstall the RAMDISK driver and paging file. 

### Setup (8 steps)

## Step 1

Enable PAE in the Windows boot options, disable hibernation in the power options for Windows, and reboot the system.  

Run the following commands in Command Prompt (cmd.exe).   Note this will force a restart in 30 seconds, so save your work.

```
bcdedit /set pae ForceEnable 
bcdedit /enum | FINDSTR pae 
powercfg.exe /hibernate off 
shutdown /r /t 30 /d p:1:1 
```

![Screenshot of command prompt usage in step 1](/images/2013-10-03-B.gif)

## Step 2

Install the commercial software [Primo Ramdisk Standard](http://www.romexsoftware.com/en-us/primo-ramdisk/index.html) by a vendor named Romex. There is a $30/seat license cost.  Romex offers a 30 day free trial.

## Step 3

Launch the Primo Ramdisk configuration program. ("%ProgramFiles%\Primo Ramdisk Standard Edition\FancyRd.exe")

## Step 4

Launch the dialog to configure "Invisible Memory Management"

Click the icon in the lower right corner of the configuration program that resembles an blue SD Card and a yellow wrench.  On the dialog, click the "Enable IM" button.  The default options worked successfully a Lenovo ThinkPad T410 (BIOS) and a Lenovo T420s (UEFI).  See the Romex documentation on front-end/back-end reserve if you experience video card problems on your hardware.

![Screenshot of configuring "Invisible Memory Management" in step 2](/images/2013-10-03-C.gif)

## Step 5
Define a new RAMDISK 

a) Take note of the maximum amount of available invisible memory as displayed in the lower right hand corner of the main window.  This will be the size of the RAMDISK.

b) Click the "Create a new disk" toolbar button to define a new persistent RAMDISK

c) Select "Direct-IO" as the disk type.  This is the faster of the two options.  Also, Credant will only ignore this device type.

d) Assign a drive letter of "Z".  This can be changed, however, a later step will need to be manually adjusted.

e) Leave "One Time Disk" unchecked to make this disk persistent across boots.

f) On the next dialog screen, enable the option for "Use Invisible Memory".  Leave all other options unchecked/disabled.

g) On the final dialog screen, select the FAT32 format and label the device "RAMDISK".

Screenshots:

![Screenshot of defining a new RAMDISK in step 5](/images/2013-10-03-E.gif)
![Screenshot of defining a new RAMDISK in step 5](/images/2013-10-03-F.gif)
![Screenshot of defining a new RAMDISK in step 5](/images/2013-10-03-D.gif)
![Screenshot of defining a new RAMDISK in step 5](/images/2013-10-03-G.gif)

## Step 6

Modify Windows' Virtual Memory settings

a) Run "sysdm.cpl" to open System Properties

b) Open the virtual memory dialog by selecting  Advanced > Performance > Settings > Advanced > Virtual Memory > Change

c) Uncheck/disable "Automatically manage paging file size for all drives"

d) Select the "C:" drive in the drive list, and select the "No paging file" option.  Click the Set button.

e) Select the "Z:" drive in the drive list, and select "Custom" size of X for initial and maximum, where X is the space available listed for the drive. You may need to slightly reduce X by ~5 megabytes.

f) Click the "Set" button and confirm your settings resemble the screenshot below.  Click the "Ok" button.

![Screenshot of modifying Windows virtual memory settings in step 6](/images/2013-10-03-H.gif)

## Step 7

Hide the Z: drive from Explorer 
  
Windows will be very annoying about the Z: drive being full.  You can hide this drive from Explorer and the common dialogs with the following registry setting.  Note you can still explicity access this drive with a full file path in any open/save dialog (e.g., Z:\folder\file.ext). If you changed the drive letter for the RAMDISK from Z: to something else, you will need to adjust the hex value of the registry key (see TechNet for the correct hex value). 

Run the following commands in Command Prompt (cmd.exe):

```
REG add HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer /v NoDrives /t REG_DWORD /d 0x02000000
REG add HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer /v NoLowDiscSpaceChecks /t REG_DWORD /d 1
```

![Screenshot of disabling Explorer disk space warnings for the new RAMDISK in step 7](/images/2013-10-03-I.gif)

## Step 8

Reboot

It's Windows, why not throw in a reboot?

## Final Thoughts

My Windows setup recommends 3 GB of virtual memory.  I'd like to try upgrading my physical RAM from 6 GB to 8GB.  This would let me add another gigabyte to the paging file.  It would also leave another 1 GB of free space on Z:.  I'm considering using this free space as a NTFS junction point with "%TEMP%" and "%SYSTEMROOT%\TEMP" to make the temp folders both fast and non-persistent between reboots.  (Junction points are the Windows equivalent of *nix symlinks for directories.  You can use the Sysinternals utility junction.exe or the Primo Ramdisk utility to define junction points.)  

I also want to test setting my IIS document root to Z: to make tests of deployment packages lightning fast (i.e., relocating the IIS document root from C:\inetpub to Z:\inetpub).  This will make disk I/O way faster for copying scores of little image and text files.  It also forces me to run an automated build/package/deploy between reboots (since Z:\ is wiped between reboots).

