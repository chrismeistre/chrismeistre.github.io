---
layout: post
title: Microsoft Windows SMB NULL Session Authentication
---

## Introduction

I come across this finding quite often, but because I normally find other ways to compromise the active directory domain, I've never really delved into how to exploit this.

At first glance, it's easy right?  The hosts are allowing you to connect to SMB without providing any credentials.  The first thing that comes to might is that there are potentials for accessing shares.  

On this engagement, because I couldn't find another way to exploit the domain, I had to delve into these findings, and it's quite interesting what I found.

## Nessus result

The below is a screenshot from Nessus indicating the finding:  

![](/assets/2022-05-25-08-12-17.png)

## Shares

The host allows you to interact with the SMB service without providing a username and password.  

The first thing I check is if there are shares available.  But no such luck:  

![](/assets/2022-05-25-08-21-44.png)

## Enumeration

I decided to fire up `enum4linux` and see what information it can extract.  I was surprised to see what I could extract.

I can get a list of all domain users:  

![](/assets/2022-05-25-08-26-03.png)

I could obtain the password policy of the domain:  

![](/assets/2022-05-25-08-27-03.png)

Why is this significant?  I could not pull this information anywhere else on the network using any other enumeration technique because I had no credentials.

## So what?

This gave me enough information to try some simple password spraying on the network.  Lucky for me, the password usage policy on this network was pretty weak, and I found credentials without triggering any account lockouts.

## Summary

Always look at new ways to move laterally on a network, even if it's findings you normally overlook as "too easy".  This easy to exploit finding turned out to be an interesting attack path.