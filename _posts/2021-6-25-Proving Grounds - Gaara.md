---
layout: post
title: Proving Grounds - Gaara
---

Gaara is a Linux box that is available on [Offensive Security's Proving Grounds](https://www.offensive-security.com/labs/individual/).  It's rated as an Easy box.

My personal opinion is that I just don't like boxes that are aimed at having brute force be the entry point, but that might just be me.

*Initial foothold:*  Password Brute Force

*Privilege Escalation:* sudo GFTOBIN

### Recon

I started off with a port scan.  I make use of [rustscan](https://github.com/RustScan/RustScan).

I have this alias set up in my .zshrc file:

```bash
portscan() {
        sudo rustscan -t 10000 -a $1 --ulimit 10000 -- -Pn -sC -sV -oA nmap/rustscan-$1
}
```

```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 3e:a3:6f:64:03:33:1e:76:f8:e4:98:fe:be:e9:8e:58 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDS8evJ7ywX5kz396YcIuR+rucTJ/OAK1SSpQoyx6Avj3v1/ZeRvikDEBZRZE4KMV4/+LraxOvCIb0rkU98B5WME6IReWvGTbF99x6wc2sDCG5haD5/OI6At8xrEQPV6FL8NqipouEeYXU5lp/aR7vsdJAs/748uo6Xu4xwUWKFit3RvCHAdhuNfXj5bpiWESerc6mjRm1dPIwIUjJb2zBKTMFiVxpl8R3BXRLV7ISaKQwEo5zp8OzfxDF0YQ5WxMSaKu6fsBh/XDHr+m2A7TLPfIJPS2i2Y8EPxymUahuhSq63nNSaaWNdSZwpbL0qCBPdn1jtTjh26fGbmPeFVdw1
|   256 6c:0e:b5:00:e7:42:44:48:65:ef:fe:d7:7c:e6:64:d5 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPFPC21nXnF1t6XmiDOwcXTza1K6jFzzUhlI+zb878mxsPin/9KvLlW9up9ECWVVTKbiIieN8cD0rF7wb3EjkHA=
|   256 b7:51:f2:f9:85:57:66:a8:65:54:2e:05:f9:40:d2:f4 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBprcu3jXo9TbgN5tBKvrojw4OFUkQIH+dITgacg3BLV
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.38 ((Debian))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Gaara
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### HTTP - Port 80

All I found here were what looked like rabbit holes.  Hidden directories, which a bunch of text on them.  I fuzzed for a while with multiple different wordlists, without finding anything.

![](/assets/2021-06-25-08-58-18.png)

### Initial foothold

I eventually resorted to something I don't like.  Because the only other port was SSH, I decided to try and just through the rockyou wordlist at it, and see if I can get a result.

I got the credentials quite quickly doing it this way.

![](/assets/2021-06-25-09-05-45.png)

![](/assets/2021-06-25-09-06-11.png)

### Privilege Escalation

I decided to just check for SUID binaries, to see if there was a quick win here.  I ended finding gdb.

![](/assets/2021-06-25-09-06-46.png)

Checking GFTOBIN, I found the method to use here.

![](/assets/2021-06-25-09-07-16.png)