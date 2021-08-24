---
layout: post
title: TryHackMe - Enterprise
---

![](/assets/2021-08-24-07-56-34.png)

After finishing Crocc Crew the day before, I decided to double down and get back to Enterprise.   I think I've previously attempted, half heartedly, to do this one, but never got far with it.  Those were pre-OSEP days, so I thought let me test to see what I know now.  It was a tough challenge, but a lot easier when I stuck to proper AD exploit methodology.

The room is called Enterprise, and is available at [https://tryhackme.com/room/enterprise](https://tryhackme.com/room/enterprise).

### Portscan

The first thing I did was a portscan:

```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Site doesn't have a title (text/html).
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2021-08-23 12:58:31Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: ENTERPRISE.THM0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: LAB-ENTERPRISE
|   NetBIOS_Domain_Name: LAB-ENTERPRISE
|   NetBIOS_Computer_Name: LAB-DC
|   DNS_Domain_Name: LAB.ENTERPRISE.THM
|   DNS_Computer_Name: LAB-DC.LAB.ENTERPRISE.THM
|   DNS_Tree_Name: ENTERPRISE.THM
|   Product_Version: 10.0.17763
|_  System_Time: 2021-08-23T12:59:29+00:00
| ssl-cert: Subject: commonName=LAB-DC.LAB.ENTERPRISE.THM
| Issuer: commonName=LAB-DC.LAB.ENTERPRISE.THM
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-08-22T12:56:47
| Not valid after:  2022-02-21T12:56:47
| MD5:   c734 51ed 4cf6 951a 6016 a435 848b 1d27
| SHA-1: 0427 5a71 a3ab 7b4c 5d69 158a 6dd3 2479 73fe b13d
| -----BEGIN CERTIFICATE-----
| MIIC9jCCAd6gAwIBAgIQbosTKBlV7oNI6hafAOP3tTANBgkqhkiG9w0BAQsFADAk
| MSIwIAYDVQQDExlMQUItREMuTEFCLkVOVEVSUFJJU0UuVEhNMB4XDTIxMDgyMjEy
| NTY0N1oXDTIyMDIyMTEyNTY0N1owJDEiMCAGA1UEAxMZTEFCLURDLkxBQi5FTlRF
| UlBSSVNFLlRITTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALma0w97
| 8ObjqThlLF3p9V0RVyyE9zjGNgiE5YhSs6OdXggVBO+wLyxyAAiZCdkRMFzYrLgL
| 6bGXrbp5GlRSMjiOmBXpp4X53ci58g1dYxwJV59bI2O9uGlvdGNcTfFPP/h+PQP6
| jsubCLtbFSPpC8MeMcFeqfehAgmpHR5WJXyu4GlxquSNcCI5s+70DShdvmGlfDbP
| X6uLeyqGnNyXElSrRBEb0ipW8xjkQBNYt6nPWfYIL0BvYMlTzPeGqDhYgn7uxPvx
| yc5NiXqJOooEetr4lTJ2Wbq/Hk3Vz+j7q9IdWd+RZtVe86fIjQqzgjp92mFYqJdo
| BeqYViJ/rUw7ZL0CAwEAAaMkMCIwEwYDVR0lBAwwCgYIKwYBBQUHAwEwCwYDVR0P
| BAQDAgQwMA0GCSqGSIb3DQEBCwUAA4IBAQA2RjxMC4wfhX+nK/C6p5pp9hb67q9v
| q8FDtsjbzOhe3QDMIb1W8XyxbIWPQg4w2j8lTae5LmpkpGW2wAQCeqeFEdSDkCih
| f/+CIWkXq8UBKMCk+cVbCtWK+gzMnxhyX8PKimzblMlYPOOWrL9pRVHcgK63p2Fn
| Xeuq1m15xsXyCXGWQ5sMuKvGQRDa4WwB1Mpi78napcBgo23EfV+wAHNWWhUHnBFt
| hMBL0NzOYsCUWLi9tu/PfyDn6Bi/pyJXuLNEtDNL+KYrGLS2h/JS61oWVH+iHlvN
| h+FnMCO1vfdtek/8XDJB5dDM40OS/PngoxmR5/IuxIhY+DUtQPxHrtsO
|_-----END CERTIFICATE-----
|_ssl-date: 2021-08-23T12:59:42+00:00; 0s from scanner time.
5357/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Service Unavailable
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7990/tcp  open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Log in to continue - Log in with Atlassian account
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49672/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49673/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49674/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49680/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49704/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49710/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: LAB-DC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

We clearly have what looks like a domain controller here.  

#### HTTP - Port 80

As per normal, we see port 80 open, and we immediately start throwing wordlists at it.  

The first interesting thing we notice is a `robots.txt` file.  

All we get is this though:

![](/assets/2021-08-24-07-59-11.png)

#### SMB - Port 445

For some reason I cannot use `smbmap` with `guest` login, but using `smbclient` manually I'm able to view shares.

I have had this before, and for this reason I've setup a simple `smbcheck.sh`, that basically just runs a number of SMB checks.  If it wasn't for this, I would've missed the open shares.

![](/assets/2021-08-24-08-00-57.png)

`smbcheck.sh`:
```bash
#!/bin/bash
if [ $# -lt 1 ]
        then
                echo -e "\nUsage: $0 ip\n"
                exit 1
        fi
ip=$1
smbclient -L $ip -N
smbclient -U "" -L $ip -N
smbmap -H $ip
python3 ~/Software/smbmap/smbmap.py --no-banner -H $ip
sudo nmap -v --script smb-enum-shares.nse -p445 $ip
```

I enumerated the shares, and found that I had access to `Users` and `Docs`.  

##### Users

I copied over all the files locally, to be able to browse and grep through the contents.

![](/assets/2021-08-24-08-07-50.png)

I found a `Consolehost_history.txt` (basically your Powershell .bash_history) file that looked interesting.

![](/assets/2021-08-24-08-08-19.png)

That password and username must lead to something, right?  Wrong.

I tried that username and password combination on everything I had at my disposal, but it did not work.

##### Docs

Here I found password protected documents.

![](/assets/2021-08-24-08-10-44.png)

I got the hashes from them, and started running it through `hashcat`.  

This turned out to be a dead-end as well.

#### HTTP - Port 7990

I turned my attention to the HTTP found on port 7990.

![](/assets/2021-08-24-08-12-31.png)

After trying to see how the logins worked, I finally re-read the message on that page.

Hoping to avoid seeing any spoilers, I headed to Google and search for `Enterprise THM`.  

Luckily for me what I wanted was listed as the first found page.

![](/assets/2021-08-24-08-13-47.png)

I headed over to the Github page.

![](/assets/2021-08-24-08-14-13.png)

Browsing the repository, I didn't find anything interesting in the code files.  But I did find a list of their developers.

![](/assets/2021-08-24-08-14-51.png)

Following the trailer, I started looking at Nik's repositories and his commits.  I came across a commit where he included a username and password.

![](/assets/2021-08-24-08-15-41.png)

![](/assets/2021-08-24-08-15-31.png)

Now this is where things got tricky for me.  I took down the username as `nic` instead of `nik`.  This led me on a wild goose chase because I was getting strange errors from SMB when using that combination.

Eventually I realised my mistake, and was able to authenticate properly.

![](/assets/2021-08-24-08-17-53.png)

#### LDAP - Port 389

With valid credentials, I was able to utilise `ldapdomaindump` to get information on the AD users.

![](/assets/2021-08-24-08-19-20.png)

Here I found a user whose password was listed as a comment/note.

![](/assets/2021-08-24-08-20-11.png)

I checked, and was still able to use these credentials.

![](/assets/2021-08-24-08-20-30.png)


#### Kerberos - Port 88

I decide to turn my attention back to the previous user, as I wasn't getting further joy from the newly found user.

I decide to see which SPNs are linked to the user.

![](/assets/2021-08-24-08-22-55.png)

Lucky for me, this dumps a hash for the `bitbucket` user.

Running `hashcat` I was able to get the password for this user.

![](/assets/2021-08-24-08-23-21.png)

I validated that the credentials were correct.

![](/assets/2021-08-24-08-23-46.png)

Going back to my LDAP dump, I look at this user and see they are able to RDP into the machine.

![](/assets/2021-08-24-08-24-36.png)

It is once logged in that I get the first flag.

![](/assets/2021-08-24-08-25-23.png)

#### Privilege Escalation

The first thing I do is get a meterpreter shell back to my Kali box.  I prefer working on that terminal, instead of through RDP.  

Once I get a meterpreter shell back, I upload winPEAS to run some privelege escalation checks.

I had to enable console colors for it to look a bit better.

![](/assets/2021-08-24-08-26-45.png)

I immediately spot two options that look easy to exploit.

![](/assets/2021-08-24-08-27-08.png)

![](/assets/2021-08-24-08-27-13.png)

Although I have access to the files for the `AtlassianBitbucket` service, I cannot control the service itself.

![](/assets/2021-08-24-08-27-51.png)

The second one is much better, and I'm able to control the `zerotieroneservice` service as well as write files to the `C:\Program Files (x86)\Zero Tier` directory.  As noted the file path suffers from a `unquoted service path` vulnerability.

![](/assets/2021-08-24-08-29-05.png)

From here I generated a `exe-service` payload using msfvenom.

![](/assets/2021-08-24-08-29-21.png)

I uploaded it to be placed in `C:\Program Files (x86)\Zero Tier\Zero.exe`

![](/assets/2021-08-24-08-29-33.png)

Setup up msfconsole to capture the shell, and got a reverse shell as `SYSTEM`.

![](/assets/2021-08-24-08-30-02.png)

The only thing left was the last flag.

![](/assets/2021-08-24-08-30-16.png)

#### Profit

![](/assets/2021-08-24-08-30-23.png)