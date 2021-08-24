---
layout: post
title: RaidForums - First Web Hacking Contest
---

I happened to come across this post on RaidForums, and decided to give it a go.

![](/assets/2021-08-24-13-17-15.png)

### Code Review

We were given a link to a Github repo, which turned out to be a fork of `flox`.

[https://github.com/Anas7asiaM00N/flox/](https://github.com/Anas7asiaM00N/flox/)

The first thing I noticed in the repo is that it's ahead 12 commits from the original project that it was forked off.  So Anas7asiaM00N did some code changes.

![](/assets/2021-08-24-13-19-16.png)

#### SQL Injection

Going through the commits, I come across this piece of code, which immediately indicates there is a SQL injection vulnerability here.

![](/assets/2021-08-24-13-20-46.png)

I'm able to establish exactly how the parameters are being processed in order to reach this point of the code.  

#### eval

The other finding I notice is that we have an `eval()` being used.  This could possibly be used to inject arbitrary PHP code.

![](/assets/2021-08-24-13-22-33.png)

In order to use this, I'll need an api_key to use the `/api/plex` endpoint that lands here.

### API access

After playing around with various payloads, I finally got the following to take advantage of the end point `/api/updates-files` to execute the SQL.

![](/assets/2021-08-24-13-25-26.png)

This gave me the output I was looking for.

![](/assets/2021-08-24-13-25-59.png)

Great, I had the `api_key` to work with now.

### Code execution

I had to determine the exact payload to use by stepping through the code, but was eventually able to get code execution with the following payload.

![](/assets/2021-08-24-13-27-24.png)

![](/assets/2021-08-24-13-27-38.png)

### The flag

Having code execution meant that I could begin with enumeration.

The very first thing I did was list the files in the current folder.

![](/assets/2021-08-24-13-28-25.png)

This file looked like the flag...

![](/assets/2021-08-24-13-28-40.png)