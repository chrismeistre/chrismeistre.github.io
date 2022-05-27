---
layout: post
title: spiderfoot - Ubuntu 22.04
---

## spiderfoot

https://github.com/smicallef/spiderfoot


## The VPS

I like making use of [Linode](https://www.linode.com/?r=f578c94010b3557b9dd05f29974ae39c049458e0) because they have servers for as little as $5 per month. Use my referral link, and you can get $100 added to your account for free when you sign up.

When I signed up this time, I decided to try out Ubuntu 22.04 LTS.

## The initial steps

The following steps can be copied and pasted while logged in as `root`.

I normally use these VPS setups as a disposable pen-testing platform and delete them after each engagement.  For that reason, I just leave all the tooling in the `/root` directory and pretty much run everything as the user `root`.  

### Get the system updated

```bash
apt -y update
apt -y dist-upgrade
```

### Install python3 and pip

```bash
apt -y install python3
apt -y install python3-pip
```

### Install spiderfoot

```bash
git clone https://github.com/smicallef/spiderfoot.git
cd spiderfoot
pip3 install -r requirements.txt
```

### Setup authentication

```bash
mkdir -p $HOME/.spiderfoot
echo 'supersecretusername:supersecretpassword' > $HOME/.spiderfoot/passwd
chmod 600 $HOME/.spiderfoot/passwd
```

### Startign up

```bash
python3 ./sf.py -l 0.0.0.0:5001
```