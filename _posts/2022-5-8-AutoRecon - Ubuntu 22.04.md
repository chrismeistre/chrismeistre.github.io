---
layout: post
title: AutoRecon - Ubuntu 22.04
---

## AutoRecon

https://github.com/Tib3rius/AutoRecon

I was introduced to this tool while I was busy with my OSCP course.  And although it was primarily made to help during OSCP exams, on small external and/or internal **whitebox** engagements, I tend to run this tool (with a modified config) to look for any low-hanging fruit while I go about my normal testing.  

On Kali Linux, it's very simple to install, and the `README.md` even has copy and paste commands.  But when installing it on any other distro, i.e. on a Linode Ubuntu VPS, there are a few more steps involved.

## The VPS

I like making use of [Linode](https://www.linode.com/?r=f578c94010b3557b9dd05f29974ae39c049458e0), because they have servers for as little as $5 per month. Use my referral link, and you can get $100 added to your account for free when you sign up.

When I signed up this time, I decided to try out Ubuntu 22.04 LTS.

## The initial steps

The following steps can basically be copied and pasted while logged in as `root`.

I normally use these VPS setups as a disposable pentesting platform, and delete them after each engagement.  For that reason, I just leave all the tooling in the `/root` directory and pretty much run everything as the user `root`.  

### Get the system updated

```bash
apt update
apt dist-upgrade
apt install python3
apt install python3-pip
```

### Install the packages that are part of Ubuntu's repo

```bash
apt install curl gobuster nbtscan nikto nmap onesixtyone redis-tools smbclient smbmap snmp sslscan sipvicious whatweb wkhtmltopdf dnsrecon python3-impacket
```

### Install SecLists wordlists from github

```bash
git clone https://github.com/danielmiessler/SecLists
ln -s /root/SecLists /usr/share/seclists
```

### Install enum4linux from github

```bash
git clone https://github.com/CiscoCXSecurity/enum4linux
ln -s /root/enum4linux/enum4linux.pl /usr/bin/enum4linux
```

### Install feroxbuster from github

```bash
wget https://github.com/epi052/feroxbuster/releases/download/2.7.0/x86_64-linux-feroxbuster.tar.gz
tar zxvf x86_64-linux-feroxbuster.tar.gz
ln -s /root/feroxbuster /usr/bin/feroxbuster
chmod a+x /root/feroxbuster
```

## The installation

Now we can get to install [AutoRecon](https://github.com/Tib3rius/AutoRecon).

```bash
git clone https://github.com/Tib3rius/AutoRecon
cd AutoRecon
python3 -m pip install -r requirements.txt
```