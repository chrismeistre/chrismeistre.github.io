---
layout: post
title: Dell Wyse 5010 Thin Client - Privilege Escalation
---

I recently had to perform a penetration test for a company that wanted to make sure the software being used for marking exams was secure.  The software was running off the Dell Wyse 5010 thin clients, accessing a web application hosted on a Windows server.

![](/assets/2022-03-18-11-10-00.png)

After spending a bit of time on the software itself, and testing the various functions within it, I came to the conclusion that there isn't a way to "cheat the system" with the software itself.

I was limited to what I could do on the thin client itself, as there have been some lockdown methods applied on the thin client.  The thin client runs on Windows 7 Standard Embedded, and the account I was logged in as was a low privilege domain user.

There were no items in the Start Menu, I couldn't open Task Manager, and a number of other actions were also locked down.  

### Command execution

The first thing I was able to do to obtain control of the thin client, was a simple "Right Click" on the Desktop, and "Create Shortcut".  This allowed me to create a shortcut to `cmd.exe` and to `powershell.exe`.  

I was able to open both `cmd.exe` and `powershell.exe` terminals.  Even though I had access to the menu option "Run as Administrator", it did nothing for my privileges when it loaded the terminal.

Powershell was also only version 1.

I could now access and browse files on the thin client, even though when you open `explorer.exe` you cannot browse any files.

### No antivirus

To my surprise, there was no anti-virus installed on this thin client.

### Physical access

I had physical access to the device, so I could plug in a USB drive with any tools I wanted to run.  

My first thought was just to run `winPEAS` on it for some easy wins.  Unfortunately, because this is such an old version of Windows, `winPEAS` kept crashing.

Powershell scripts also were blocked from loading, so I couldn't use any of the normal enumeration tools.

### SharpUp

After searching a bit online, I found [SharpUp](https://github.com/GhostPack/SharpUp).  It's basically the c-sharp version of `PowerUp.ps1`.  I compiled it, and ran it.

It identified two services that I could exploit.  The first being `SNMP`, one of the standard Windows services, and another was `HAgent`, which is the service used by Dell Wyse Device Manager that is installed on the thin clients.

### HAgent

To exploit this service, I was able to modify the `C:\Program Files\WDM\HAgent.exe` file.  I created a simple console application in c-sharp that would just add a local user, and add them into the administrators group.

I copied it over, and restarted the thin client.  Once I was logged back in, the file executed and added my new local administrator user.

### SNMP

The exploitation of this service involved editing the registry settings.  I didn't have access to overwrite files, but I could do whatever I wanted to in the registry settings for this service.

I changed the `ImagePath` in `HKLM:\System\CurrentControlSet\Services\SNMP` and used the following payloads:

`cmd /c net user add pentest <PASSWORD>`  
`cmd /c net localgroup administrators pentest`

Each time I modified the settings, I had to restart the thin client so that the service would also restart to execute my payload.

### Why all this trouble?

With local administrator access, I could make use of `mimikatz` to download the local administrator password hash.  And once I had this, I could test the password hash for password reuse against another +/- 120 thin clients.  Fortunately for me, the same password was being used on all the thin clients.

This opened an attack opportunity:  
- Install keyloggers on all thin clients to capture login details of other users (and administrators)
- Re-use these credentials to access the software as a different user or as an administrator

### Will this work on other Wyse 5010 Thin Clients?

My understanding was that the thin clients I was working with, contained the standard factory installation as they come.  It's worth noting that I have do not have access to these devices outside of this engagement and it's quite possible that the vulnerabilities were introduced by the IT department themselves.