---
layout: post
title: SQLite Encryption - Data leak
---

During a recent engagement, we had to do a black box assessment of a software package that is used to monitor candidates while they take exams.  The software provides a sandbox environment, and at the same time monitors various processes and actions performed on the computer at the time of taking the exam.

I loaded the binary files into `dnSpy`, and started looking at the source code that I could access.  It appeared that some obfuscation was being used, as there were parts of the code that weren't easily readable.  There was however a big chunk of code that I could still read, so I was able to trace through what was happening through static analysis.  

One of the things I noticed was that the software was using two SQLite database files.

![](/assets/2022-03-09-12-43-33.png)

I thought it was going to be an easy win until I realised the database file itself is encrypted.  

![](/assets/2022-03-09-12-41-26.png)

After digging around in the source code that was available and going down a few rabbit holes, I finally noticed a file in the folder.

![](/assets/2022-03-09-12-46-10.png)

A bit of research reveals that SEE (SQLite Encryption Extension) is used to encrypt the SQLite database.  More information about it can be found on https://www.sqlite.org/see/doc/trunk/www/readme.wiki.

Unfortunately, much more digging did reveal a way for me to obtain the password used to encrypt the database, but I wasn't able to figure out a way to actually get the password.

### Memory dump

It occurred to me, that at some point this database must be in an unencrypted form in memory so that the queries can be performed against it. 

What I did then was to open up Task Manager while the application was running, and use the "Create dump file" feature.

![](/assets/2022-03-09-13-28-51.png)

#### Sandbox 

To get to the point of being able to open Task Manager, took a bit of a workaround as well.  

It's worth noting at this point that I hadn't found a way to escape the sandboxed environment yet.  What was available to me was the application that was running in full screen, with all shortcuts like "Windows key" and Alt+Tab disabled.  

What I did however figure out is if I use the "Always on top" feature of Task Manager, and I hit Ctrl+Alt+Del while in the application, I could reach the Task Manager at least.  From here I was able to create the dump file.

### Analysing the file

My first idea was to run strings against the dump, and just check what all is available to me.  This provided a list of 4,811,739 strings, which would take too long to process manually.  

At this point, I had found cleartext credentials via other methods, so I was looking to see if I could find them in the memory dump.  My reasoning was if I could find them, then I might need to start looking for cleartext passwords in the memory dump.  Unfortunately, for reasons not known to me right now, I could not find any of the 3 passwords I knew were being used by the software to perform functions like HTTP requests.

I found a lot of other interesting things though.

### Scalpel

I knew I was on to something because I noticed sections of SQL statements in the memory dump:

![](/assets/2022-03-09-13-53-39.png)

`scalpel` is a utility that will go through a raw file, and find other files within that file.  It does it through a process called `file carving`.

It does this by looking for known file types and their headers.

You can look at https://www.sqlite.org/fileformat.html under the section `Magic Header String` to find the "start" of a DB file.  

I found that I could use the following as a header configuration in `scalpel` for an SQLite database.

```
db        y       5000000 \x53\x51\x4C\x69\x74\x65\x20\x66\x6F\x72\x6D\x61\x74\x20\x33\x00 
```

When running `scalpel` on the memory dump file, it recognised and saved 4 database files.

![](/assets/2022-03-09-14-04-51.png)

Upon inspecting the files, I found that they were indeed SQLite database files.

![](/assets/2022-03-09-14-06-12.png)

### The problem

The only problem was there was no data in the tables.  

The question is now, was there no data in these two encrypted database files to begin with, or was `scalpel` just not successful in obtaining the actual data.  Having a look again at the database file sizes, it's quite possible that there was never information inside these databases to begin with.  

**The solution?**  Right now, I'm not sure.  But being able to access the structure of the database, helped me in a different section of the code to successfully compromise the exam software.  When I have a chance, I will set up a PoC application to determine if this method will actually extract data as well.