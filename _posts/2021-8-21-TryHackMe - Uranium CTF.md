---
layout: post
title: TryHackMe - Uranium CTF
---

![](/assets/2021-08-21-14-35-10.png)

I enjoyed this room quite a bit.  It was a little different than the normal rooms I have gotten used to on Tryhackme.  It made me think a little of my OSEP (Offensive Security Experienced Penetration Tester) journey. 

The room is called Uranium CTF, and is available at [https://tryhackme.com/room/uranium](https://tryhackme.com/room/uranium).

### Portscan

The first thing I did was a portscan:

```
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 a1:3c:d7:e9:d0:85:40:33:d5:07:16:32:08:63:31:05 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMwJfFdIx+ajk4m+SaA9FCONx/arQgXZx22oViZpzp6QSuMYI3u4GXubPf+P/1AKjrdTZ2UtLt3HszSNuf3V/RMQgvXYrPGFmClvfnZZ88an/oz38l4aGTnZ1LJ8upLU90METx4YXcA9uM3u0dECXfUMqFHX+wwFxP/WKUJ7lX3Ae7H+Uj2Bwrw76d8Ndwf3a/EDZ6gTzYTgrgprZQeBbriJM9yrjljakLNCajdDzjtDSQs+wXwme2MXx8u7aAZ4ofL7cuGxCPil2R92HWrKomMQ7Iyd9SMre3rCLhSOhbYnJGTwl3P6fEqCPqp2shMO2AYVrgz0jC6ou8iM3jGe4t
|   256 24:81:0c:3a:91:55:a0:65:9e:36:58:71:51:13:6c:34 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBZPRLpPW1xp0xWpgkGvpFwR6tKPTMRvjkAbiwoPC/qCKUYg2p06XDFCMHNDmuqIC5SHvnqZqM0EdwJIuUkFvIE=
|   256 c2:94:2b:0d:8e:a9:53:f6:ef:34:db:f1:43:6c:c1:7e (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFY55KAy8LZ+FNH0gc/IzoPlL/gQDwtvUMTzmQTd8MAj
25/tcp open  smtp    syn-ack ttl 63 Postfix smtpd
|_smtp-commands: uranium, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, 
| ssl-cert: Subject: commonName=uranium
| Subject Alternative Name: DNS:uranium
| Issuer: commonName=uranium
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-04-09T21:40:53
| Not valid after:  2031-04-07T21:40:53
| MD5:   293d bef3 2fee 6092 c0d7 2a67 ea27 367c
| SHA-1: 0a0c 26e0 ae3c 723e 538d 3c21 6b40 c84c f9e7 8fdb
| -----BEGIN CERTIFICATE-----
| MIIC0zCCAbugAwIBAgIUIVXdlC2OCz8mRhqtv01MouzQ0ZswDQYJKoZIhvcNAQEL
| BQAwEjEQMA4GA1UEAwwHdXJhbml1bTAeFw0yMTA0MDkyMTQwNTNaFw0zMTA0MDcy
| MTQwNTNaMBIxEDAOBgNVBAMMB3VyYW5pdW0wggEiMA0GCSqGSIb3DQEBAQUAA4IB
| DwAwggEKAoIBAQCpxCDhZoI2WVRkeoeXHBA1Y3LnA0WNjAnH1HyeYwzhKeVekmip
| m3bzvH0e3Z9D9zyf1mnhYnV4i4yA8I+Jp/Cx1Gc9VXvD2cAW4azHdCZBjR6arGCF
| 14gxtdrgiBSdKoMqUo2T9tlfqfnrGOTcc70KYXBJ6tjIHPrFmeXRUvlZWhsF0i1R
| zWqWLNB3Wy7O2yYP2SV8MLjoEGi2ZeqSMbYkhMKTbS7VSLNISO9ax2Wxb5j5lELX
| jLox6/nPueJkLR37YbjDztdZ3Lpz8FXUqymz+OWZq2MLYfde2Zn7cA7zFgeCfOJM
| HhGN9BC046EBW60RVFhWaczTHsRALnWvQ5VfAgMBAAGjITAfMAkGA1UdEwQCMAAw
| EgYDVR0RBAswCYIHdXJhbml1bTANBgkqhkiG9w0BAQsFAAOCAQEAj1F/S1v2EFAL
| H1FG/SWNlqsD9KKwUDSceiHicEz8IE9YU+Vg1NRxluYYpkDbfyrCVBPW//JZJNd2
| jpCObLaQRxZ/4QCa+t4/7Nlue8IiWzax8nEVMUV8clFGlBmktfsx7d/iyjDeGq2H
| VE3p6nFpZFmGmCvYfue9IcZWduFbOIWzf2XvnGnaHxYvccBry7tFGW5F93i3asV3
| UQqT8xZ+eaxzijdoEl9klp/Ee4R2b8bjHMDt7SFzvQAGzL3j1mFPY9qA78K9eNv3
| vHgqdChT9jryHVBEcLiTTPsfNRcARQeOr4O0wGdlQX6E3FRbPn3JpM96Do8+/kJd
| r/RWkJhbQQ==
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: HEAD GET POST OPTIONS
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Uranium Coin
Service Info: Host:  uranium; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

We had a limited attack surface here.

### HTTP - Port 80

I browsed around here for a bit, did some fuzzing but couldn't find anything useful.

### SMTP - Port 25

Going back to the Tryhackme profile page of this challenge, I went to go read the description again.  This time I noticed that it said this challenge would involve phishing.  I turned my attention to port 25, SMTP.

#### Recon

Reading the description of the challenge a bit better, also showed that there was a Twitter account associated with this challenge.

The Twitter account gives useful information in the form of tweets and screenshots.  The first being the domain name, and the second being that you are able to determine what email address to attack.  The third, and most important, is how this person is responding to emails he receives.

![](/assets/2021-08-21-14-46-08.png)

![](/assets/2021-08-21-14-46-20.png)

![](/assets/2021-08-21-14-50-40.png)

#### The attack

This gave me enough info to start my attack.  

I first created a simple reverse shell in C.  I based this assumption off the fact that it mentions he will open applications in the terminal.  The screenshot also indicated that it is a Linux machine.

![](/assets/2021-08-21-14-52-32.png)

![](/assets/2021-08-21-14-52-58.png)

Swaks is a nice command line tool to send out emails.  

Sending the email with [swaks](http://www.jetmore.org/john/code/swaks/):

![](/assets/2021-08-21-14-51-56.png)

After a few moments I got a reverse shell back as `hakanbey`.

![](/assets/2021-08-21-14-53-47.png)

The first thing I do is put a ssh key on the machine, so that I can ssh in and have a proper shell.

![](/assets/2021-08-21-15-02-22.png)

Here I was also able to capture the first user flag.

![](/assets/2021-08-21-15-14-20.png)

### Password for the chat application

I spent some time trying to reverse engineer the binary for the chat application.  After being in this rabbithole for a while, I remembered that I completely stepped the initial enumeration step after gaining a foothold.

Doing some enumeration, I come across a pcap file in the /var/log directory.

![](/assets/2021-08-21-15-03-12.png)

Opening up the file in Wireshark, you're able to view the conversation from the log.

![](/assets/2021-08-21-15-04-15.png)

### Interacting with the chat application

After interacting with the application, and keeping in mind this challenge seems to be about phishing, I finally manage to determine what to enter to get `kral4` to give up my password.

![](/assets/2021-08-21-15-08-32.png)

### kral4

Now that we have the password, we are able to make use of the sudo privileges that we are assigned.

![](/assets/2021-08-21-15-10-02.png)

![](/assets/2021-08-21-15-10-25.png)

Here I was able to read the second user flag.

![](/assets/2021-08-21-15-17-26.png)

### Web flag

I had already determined during my initial enumeration that we had the dd binary that is SUID to run as `web`, and the `kral4` group had execution permissions.

![](/assets/2021-08-21-15-12-32.png)

![](/assets/2021-08-21-15-13-21.png)

Using this I was able to read the web flag.

![](/assets/2021-08-21-15-13-00.png)

### Root flag

Reading an email that has been sent to `kral4`, it seems we need to place a nano binary in our home folder, and `root` will set it SUID for us.

![](/assets/2021-08-21-15-20-39.png)

I then also wrote to `index.html`, because there is a script running if you look at ps which seems to be reading that file.  Perhaps it monitors it for changes?  Check the script once you have rooted the machine.

![](/assets/2021-08-21-15-33-51.png)

I tried setting a random file to nano and see if it will SUID it, but I got this message.

![](/assets/2021-08-21-15-21-16.png)

By copying the correct nano binary to my home folder, I get a better result.

![](/assets/2021-08-21-15-34-36.png)

![](/assets/2021-08-21-15-36-03.png)

![](/assets/2021-08-21-15-42-49.png)

Reading the file using `./nano /root/root.txt`

![](/assets/2021-08-21-15-41-01.png)

### Profit

![](/assets/2021-08-21-15-44-09.png)