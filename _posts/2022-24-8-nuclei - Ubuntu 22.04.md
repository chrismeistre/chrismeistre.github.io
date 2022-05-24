---
layout: post
title: nuclei - Ubuntu 22.04
---

## nuclei

https://github.com/projectdiscovery/nuclei


## The VPS

I like making use of [Linode](https://www.linode.com/?r=f578c94010b3557b9dd05f29974ae39c049458e0) because they have servers for as little as $5 per month. Use my referral link, and you can get $100 added to your account for free when you sign up.

When I signed up this time, I decided to try out Ubuntu 22.04 LTS.

## The initial steps

The following steps can be copied and pasted while logged in as `root`.

I normally use these VPS setups as a disposable pen-testing platform and delete them after each engagement.  For that reason, I just leave all the tooling in the `/root` directory and pretty much run everything as the user `root`.  

### Get the system updated

```bash
apt update
apt dist-upgrade
```

### Install Golang

```bash
wget https://storage.googleapis.com/golang/getgo/installer_linux
chmod +x ./installer_linux
./installer_linux
```

```bash
source ~/.bash_profile
```

### Install nuclei

```bash
go install -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei@latest
```

### Update nuclei

```bash
nuclei -update
nuclei -ut
```