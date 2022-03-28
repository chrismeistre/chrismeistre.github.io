---
layout: post
title: How to pass your OSED exam
---

**How to pass your OSED (Offensive Security Exploit Development) certification exam.**

I thought I would share what my OSED journey was like, and all I did to prepare for and pass the grueling 48-hour certification exam.

Initially, I thought 60 days of lab time was going to be enough...

### Before the course

I had been on a streak doing my OSCP, OSEP, and OSWE all in the year, so whether or not it was relevant, I felt that I was in the Offensive Security course and exam mindset.  I had taken to the way that they present topics and how the course notes and exams are structured.  I think knowing how they approach their exam questions helps prepare you mentally for expecting what you don't expect.  The only thing you can be sure of is that you're going to have to try harder.

A couple of weeks before I was going to start with the course, I decided to sign up for [Assembly Language Adventures: Complete Course](https://www.udemy.com/course/x86-asm-foundations/).  I have been developing since a very young age, so development is in my blood.  There was a point in my life when I could easily make use of assembly language for development, but that was a long time ago.  After finding a recommendation for this course, I decided to go through this course in preparation.  

I knew that a big part of this course is going to involve reading and writing assembly language code.  I worked through the course and made notes for myself to reference when I needed to.  I believe doing this course was invaluable and allowed me to work through the course notes more quicker.

### During the course

My approach to Offensive Security courses has always been the same.  I take the PDF manual (anything from 600-1000 pages) that we are given and go have it printed out on the day my lab time starts.  

I then sit with the printed-out notes in front of me, the video version of the course notes playing on one of my monitors, and I have another monitor where I meticulously follow along.

Whenever there is an exercise to do, I do it and make sure I understand it.  There are also always extra mile exercises, which are meant to challenge you a bit more on the subject.  These almost always require a bit more time and further research in order to complete them.

This process takes a long, and while going through this course I did feel under pressure most of the time that I might run out of time to complete the challenges in the lab.  It was however important for me to know that I completed all the exercises and all the extra mile exercises as well, before heading into the exam.

While going through the course material, I also make a note on my whiteboard about any topic I struggled with.  This is meant as a "to research further" list before the exam.

I found the course quite tough, definitely the most difficult of the ones I have done.

### The challenges

As with all the other courses, OSED also had challenges in a lab that you had access to, in order to test your skills and methodology before you attempt the exam.  These challenges are meant to be tough and to thoroughly test that you've absorbed the course content.  

There were three to complete, and if I remember right, I had 2 weeks left of my lab time.  

#### The first challenge

I decided to just start on one, and see how things go.  Immediately I felt like I was out of my depth, and imposter syndrome set in.  I felt like I had learned nothing during the course, or at least I did not understand how to apply it to actually find a vulnerability, and develop an exploit for it.

I must have spent about 2 or 3 or 4 full days just trying to reverse engineer the application that we were given, trying to find a vulnerability.  Eventually, I decided I need to ask for help because my time is running out.  The Discord channels available to the students are awesome, and there is always someone in the community willing to help out.  

Searching through the history, I immediately found that the challenge I was working on was unofficially rated the most difficult one.  I felt a bit relieved knowing that, and decided to attempt to do a different challenge first.

#### The second challenge

As I started with this challenge, I was able to immediately gain traction, and make quite a bit of progress on it.  It was definitely the confidence booster I needed.  While busy with this challenge, I realised that my ROP (Return-oriented programming) skills were a bit lacking.  

I went back to the chapter about this and redid all the exercises and extra miles.  By this time I had found https://www.corelan.be/index.php/articles/, and I read through almost all their articles.

Once I had done a bit of playing around with building ROP chains, I came back to the challenge.  It took me a while, but I eventually figured it out and had my ROP chain working perfectly.

I had finally developed my first exploit "on my own", and obtained a reverse shell from it.

#### The third challenge

By this point, I was concerned that I would not be able to finish the three challenges before my lab time runs out.  This would be a mental block for me going into the exam because based on my previous experience, I know the challenges are going to prepare me for the exam.

I decided to just put my head down, and see how far I could get with this.  As the applications we were attempting to exploit are all publicly available applications, I decided to search and make sure I find the specific versions and keep copies of them.  Worst case, if the lab time runs out, I could install local copies and attempt to finish the outside of the lab time.

The third challenge was also quite difficult for me, but after what felt like days, I eventually found the vulnerability.  This time I did not need to refresh any skills though, and I was able to go directly into writing the exploit.

#### Back to the first challenge

Coming back to this challenge with a more positive attitude after being able to accomplish the previous two challenges, I was finally able to make headway.

I had finally found the vulnerability and successfully created an exploit for it.  Afterward when I was discussing my approach with a fellow student, I realised I had taken the "long way round", and probably spent 4-6 hours more on developing the exploit than I should've.  I just shrugged it off as extra practice though, because I had finally finished all three challenges, with about one day left of my lab time.

#### Extra challenges

I knew I wasn't ready to take on the exam, so I started asking around and searching for more "challenges" to do.  Reverse engineering was going to be a difficult thing in the exam, so I decided that I would rather focus on applications that have known vulnerabilities, with proof of concepts.  

That way I could practice things like ASLR, DEP and SEH bypass, ROP chain building, etc.  

My methodology was that I would search on exploitdb.com for vulnerable Win32 apps, download the POC and attempt to find the application to download.  Finding the applications (the specific vulnerable versions) turned into a lot bigger search than I thought.  I set up a completely separate Virtual Machine that had no access to the host computer because some of the applications I downloaded came from unknown sources.

Once I had found the correct version of an application, I would glance at the first few lines of the POC.  This gave me an indication of where the vulnerability exists.  Most of the time it would be an input field or parameter that you could overflow in order to create a "crash".

I would then attempt to go through all the steps required to successfully build my own exploit for the application.  I did the first run-through without any additional Windows security checks (ASLR and DEP) turned on.

If I was able to create the exploit, I would attempt to do it with just ASLR turned on.  I would then attempt it with just DEP turned on.  And lastly, I attempted to do it with DEP and ASLR turned on.

It's worth noting that sometimes is just not possible to do it with DEP and ASLR turned on.  For ASLR, you need some way to leak an address, and an application might not always have such a vulnerability.  For DEP you need to have access to ROP gadgets, in order to build an ROP chain.  I stuck with just using OS independent files, so it was not always possible to get it working.

I did have some fun, even on the ones where I couldn't bypass the DEP protection for example.  Even if you fail the final goal, you are still learning how to work with various gadgets.

This was the most important part of the course for me because going through all these extra applications helped me build confidence.

The three exercises I definitely recommend doing are:  
https://github.com/bmdyy/quote_db/ (ASLR & DEP)  
https://github.com/bmdyy/signatus  
https://github.com/stephenbradshaw/vulnserver

Here is a list of the rest of the applications I practiced on:
- 10-Strike Network Inventory Explorer 
- 10-Strike Network Inventory Explorer (DEP)
- Blaze DVD Professional 6.1.1.6
- CastRipper
- Coolplayer 2.19.4
- Easy RM to MP3 2.7.3.700
- EasyFTP 1.7.0.2
- FreeFloatFTPD
- FreeFTPD
- Knet 1.04
- Kolibri Host 2.0
- Kolibri Host 2.0 (DEP)
- Streamripper 2.6
- Triomp
- VulnServer-GTER
- VulnServer-GTER (socket reuse)
- VulnServer-GMON (DEP)
- VulnServer-KSTET (Egghunter)
- VulnServer-LTER (Custom Shellcode)
- VulnServer-Trun
- VulnServer-Trun (DEP)

#### Helping other students

It was while doing extra exercises, that I also started monitoring the OSED channels on Discord to see if any other students needed help.  I always offer my help, not because I think I know better than them, but because it gives me an opportunity to discuss things with a fellow student.  Discussing the work helps me understand the material better, and at the same time I feel like I am contributing back to the community.

#### The exam

After all the above, I finally set my exam date to take on this 48-hour challenge.  I know it was going to be challenging, and I wasn't disappointed.  

I felt relatively confident going into this exam, but I knew it was going to be a fight from the start to the end.  

The points for the exam are set out in such a way, that you need to be able to complete 2 out of 3 tasks successfully.  

About 24 hours into the exam, I had successfully completed two of the tasks.  After spending a few hours on the third task, I decided to rather take time while I still had access to the exam environment to write my report.  That way, if I need to retest or take extra screenshots, I have plenty of time.  And I knew that with the two tasks, I would be successful.  

Once I was happy with my report, I carried on with the third task.  This was quite a difficult one, and I finally found a vulnerability and a path on how to develop the exploit.  Unfortunately, I only had about 3 hours left on the exam at this point.  I gave it my all during those 3 hours, but I was not able to get it done in time.  

#### The summary

It's in my view that if you have done all the exercises (including extra miles) in the course notes, and the three challenges in the lab, you will have enough knowledge to be able to complete the exam successfully.  

The exam is tough, and you need to think outside the box... for me, it felt way outside the box.  But it's definitely achievable.

#### Resources

https://drive.google.com/file/d/1W5uqB5ScrpHS3Tu3P7Veh1SB5x02_wsY/view

https://www.corelan.be/index.php/articles/

https://github.com/bmdyy/quote_db/

https://github.com/bmdyy/signatus

https://github.com/stephenbradshaw/vulnserver