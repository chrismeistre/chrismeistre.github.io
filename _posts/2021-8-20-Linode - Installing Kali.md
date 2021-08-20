---
layout: post
title: Linode - Installing Kali Linux
---

It's often needed for me to spin up a Linux box at a VPS provider, to help speed up recon during an engagement.  It also helps testing infrastructure and web application from different IP addresses without having to switch between VPN connections.

I finally looking for a simple way to get Kali Linux running in a Linode instance, thanks to https://www.youtube.com/watch?v=JrMcbyI0qPw.

You can do this on a Debian 10 instance.

Here is the TL;DR of it:

```
apt update && apt-get install net-tools && apt-get install gnupg

wget -q -O - https://archive.kali.org/archive-key.asc | gpg --import

echo "deb-src http://http.kali.org/kali kali-rolling main non-free contrib" | sudo tee /etc/apt/sources.list

apt-get update && apt-get install kali-linux-everything

```

