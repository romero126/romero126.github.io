---
layout: post
title: Windows Print Services in a not-XP world
---

Recently I've had the "pleasure" of troubleshooting Windows print services... You see, we use a CUPS server and samba on Linux to host our printers. We then have a script look at the samba server and map to the CUPS printers using IPP printing. It would seem that on 64 bit Windows 7 (and probably newer, and maybe 32 bit, but we don't use that one...) Occasionally printing to IPP will just randomly fail (all print jobs show up as error and can't be resumed or anything). To resolve this issue, we have a script that removes all network based printers, and then deletes all unused print drivers. We then remap the printers and everything kinda sorta works. Except when we have to do this "fix" every day or two.

I know what you're thinking, because I was thinking it to. If the print jobs are failing, then you can simply look in the Event Viewer and find what the issue is... Good news! There is a Microsoft-Windows-PrintService/Debug log! Not so good news... It's not enabled by default :(

No fear! It's actually pretty easy to enable the log. It's just a wevtutil command:

`wevtutil sl Microsoft-Windows-PrintService/Debug /e:true /q:true`

* [wevtutil](https://technet.microsoft.com/en-us/library/cc732848.aspx): this is the name of the Windows Event Utility
* sl: this is short for the command set-log
* Microsoft-Windows-PrintService/Debug: Well, this is the name of the event logs that we want to set
* /e:true: This is short for /enable:true which enables the log
* Word of caution: Enabling the log truncates it, so anything that is already in there will be lost.

So now that you've enabled debugging, comes the fun part... Waiting for the issue to re-occur. Naturally it would seem that enabling this logging will cause the printing to never have an issue again. Which hey, if all it costs is logging into a maximum 1 MB log file, then this is a pretty cheap solution to a potentially large issue.
