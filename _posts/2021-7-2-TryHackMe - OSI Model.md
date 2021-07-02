---
layout: post
title: TryHackMe - OSI Model
---

![](/assets/2021-07-02-12-08-46.png)

This isn't necessarily a walkthrough for this room, but just to document the fun I had with the little game that comes with it.  Hacking the little javascript game is not part of the challenge though, but hackers always want to know how things work.

The room is called OSI Model, and is available at [https://tryhackme.com/room/osimodelzi](https://tryhackme.com/room/osimodelzi).

Once you have completed all the questions for the room, you're presented with a little game they developed to test your knowledge.

I skipped right to looking at the source code of the game, seeing there was a challenge laid down that said their staff completed it in 19 seconds.

The below section is a snippet of what I think is the most important function.

![](/assets/2021-07-02-12-11-52.png)

Here the logic is built to determine if you're going into the right room, based on what level you are on.

So what if I just set my level, and pretend I go into the right room on the last level?

![](/assets/2021-07-02-11-53-16.png)

Great, I got it in 1.05 seconds.  Still, I could do better by cutting down on the time I spent between clicking Begin and executing the javascript code.

I browsed through the code again, to determine what the start button fires off.  

![](/assets/2021-07-02-12-15-11.png)

Great, that's easy enough to replicate.  I refreshed the page, and executed the code.

![](/assets/2021-07-02-12-00-25.png)

0.25 seconds!

I could also just skip all the logic, and just display the Game Complete box.

![](/assets/2021-07-02-12-18-40.png)

![](/assets/2021-07-02-12-19-34.png)

Anyways, not a very sophisticated hack, but still fun!

I also noticed a cheat code (ala Konami) in there

![](/assets/2021-07-02-12-21-20.png)