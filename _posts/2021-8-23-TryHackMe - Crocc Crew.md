---
layout: post
title: TryHackMe - Crocc Crew
---

![](/assets/2021-08-23-13-35-06.png)

This is another one of those challenges I could test my Active Directory skills that I honed during OSEP.  It was a tough challenge, and what made it tougher was the unusual way you get to the initial creds.

The room is called Crocc Crew, and is available at [https://tryhackme.com/room/crocccrew](https://tryhackme.com/room/crocccrew).

### Portscan

The first thing I did was a portscan:

```
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-server-header: Microsoft-IIS/10.0
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2021-08-22 04:58:15Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: COOCTUS.CORP0., Site: Default-First-Site-Name)
|_sslv2-drown: 
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
|_ssl-ccs-injection: No reply from server (TIMEOUT)
|_sslv2-drown: 
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: COOCTUS.CORP0., Site: Default-First-Site-Name)
|_sslv2-drown: 
3269/tcp  open  tcpwrapped
|_ssl-ccs-injection: No reply from server (TIMEOUT)
|_sslv2-drown: 
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_sslv2-drown: 
9389/tcp  open  mc-nmf        .NET Message Framing
49666/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49672/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         Microsoft Windows RPC
49679/tcp open  msrpc         Microsoft Windows RPC
49711/tcp open  msrpc         Microsoft Windows RPC
49885/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

We clearly what looks like a domain controller here.  

#### HTTP - Port 80

As per normal, we see port 80 open, and we immediately start throwing wordlists at it.  

The first interesting thing we notice is a `robots.txt` file.

![](/assets/2021-08-23-13-39-39.png)

Awesome, looks interesting.  

![](/assets/2021-08-23-13-40-39.png)

Wow, that was easy.  We already have creds and the fuzzing is still running.  

And this is where the hour long rabbit hole started.  

I tried through `crackmapexec` at it, and it didn't want to accept the credentials.  

This led me to believe there might be other virtual hosts running here, and I kept throwing things at it.  Nothing yielded results.

In the meantime the fuzzing didn't turn up anything else, but a dead-end `backdoor.php`.

So what do we do when we get stuck?  Try Harder... and enumerate more!

### MSRPC - Port 135

The only thing I could get that showed anything interesting, were `rpcdump.py` and `tcp_dcerpc_auditor`.

![](/assets/2021-08-23-13-45-13.png)

![](/assets/2021-08-23-13-45-18.png)

![](/assets/2021-08-23-13-45-22.png)

The above output seemed promising, because I was at least getting something to work with.  

Another few hours were wasted in this rabbit hole.  The plus side is that I think I know RPC a little better now.  I might have missed something here, and am looking forward to seeing other people's solutions.

Also tried to see if I can pick up a IPv6 IP address.

![](/assets/2021-08-23-13-55-56.png)

### Kerbrute - Port 88

I decided to through a wordlist are kerbrute, and see what interesting users I get.

![](/assets/2021-08-23-13-53-02.png)

After getting a valid user list, I already tried running it through `crackmapexec` with the password obtained from the earlier found db credentials.  Nothing worked.

### RDP - Port 3389

I turned my attention to RDP.  I know rdesktop spits out some useful info while the connection is trying to establish, but besides a hostname which I already had, there wasn't much.

I finally had a breakthrough, after giving up the night before, when I logged back into RDP.  I think it happened by fluke, because I typed in the params wrong for rdesktop.  It gave me an idea, and eventually I was presented with the following.

![](/assets/2021-08-23-13-51-32.png)

I recognised what looked like a username on the stickynote from my earlier enumeration.  Immediately trying it using `crackmapexec` I had finally gotten somewhere.

![](/assets/2021-08-23-13-55-16.png)

### SMB - Port 445

Using the found credentials, I circled back to doing all my initial enumeration again.  But before that I logged into SMB to at least get my first flag.

![](/assets/2021-08-23-13-56-47.png)

![](/assets/2021-08-23-13-57-23.png)

Using `enum4linux-ng` I was able to get more users.  A trimmed down version is below.

```
'1115':                                                                                       
  username: mark                                                                              
  name: Mark                                                                                  
  acb: '0x00020010'                                                                           
  description: (null)                                                                         
'1121':                                                                                       
  username: admCroccCrew                                                                      
  name: admCroccCrew                                                                          
  acb: '0x00000210'                                                                           
  description: (null)                                                                         
                                                                      
'1124':                                                                                       
  username: cryillic                                                                          
  name: cryillic                                                                              
  acb: '0x00000210'                                                                           
  description: (null)                                                                         
'1129':                                                                                       
  username: Varg                                                                              
  name: varg                                                                                  
  acb: '0x00000210'                                                                           
  description: (null)                                                                         
'1134':                                                                                       
  username: password-reset                                                                    
  name: reset                                                                                 
  acb: '0x00040210'                                                                           
  description: (null)                                                                         

```

I turned my attention to `ldapdomaindump`, and was able to immediately spot something that looked familiar from my OSEP learning.

![](/assets/2021-08-23-14-00-22.png)

We had a user with TRUSTED_TO_AUTH_FOR_DELEGATION.

During the course we almost exclusively made use of Rubeus, and I decided to take this time to learn how to accomplish all this from my Kali box, using `impacket`.

It took me a while of trying different things, at failing at it, until I finally figured out what was going wrong.  I needed the correct SPN.

I found this very cool writeup.  [http://blog.redxorblue.com/2019/12/no-shells-required-using-impacket-to.html](http://blog.redxorblue.com/2019/12/no-shells-required-using-impacket-to.html)

Making use of `pywerview` I got this user's details.

![](/assets/2021-08-23-14-03-35.png)

Knowing what the SPN was, I as able to create a ticket that impersonates Administrator.

![](/assets/2021-08-23-14-04-33.png)

![](/assets/2021-08-23-14-04-53.png)

Using this ticket, I was able to dump hashes from the DC.

![](/assets/2021-08-23-14-05-26.png)

![](/assets/2021-08-23-14-05-33.png)

I immediately used the Administrator's hash, and was able to get code execution.

![](/assets/2021-08-23-14-05-58.png)

Making use of the web_delivery module in msfconsole, I was able to get a meterpreter shell.

![](/assets/2021-08-23-14-06-41.png)

![](/assets/2021-08-23-14-06-45.png)

### Oh yes... the flags!

I had gotten so tied up in getting the ticketing working, and doing privilege escalation, I had forgotten about the flags.

Once I was Administrator though, I was able to get all the flags.

This felt like cheating though... so I am sure there was a more intended way.

![](/assets/2021-08-23-14-08-15.png)

![](/assets/2021-08-23-14-07-18.png)


