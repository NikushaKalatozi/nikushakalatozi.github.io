---
layout: post
title:  "CyberCube2018 EternalRomance Walkthrough"
date:   2018-05-10 21:07:13 +0400
categories: jekyll update
---
Hi, today we're gonna deal with the vulnerable machine "Eternal-Romance" which I have exploited on cyber security olympiad in Georgia called "CyberCube 2018"
Special thanks to Georgian CERT for organizing such events & olympiads annually!<br/> (btw prizes for first 3 place were horrible but it's ok xD fuck it nvm)

So let's begin
Today’s covered topics will be:
 - Various Port Scanning methods (my personal preference)
 - Port Analysis
 - Blind SQL Injection
 - SMB Service Enumeration
 - DNS Zone Transfer Attack
 - Remote Code Execution

Port scanning gave us result: 
<br/>
{% highlight text %}
root@kali:~# nmap -Pn -sV 192.168.88.236

PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.7 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http        Apache httpd 2.4.7 ((Ubuntu))
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
MAC Address: 08:00:27:D2:91:27 (Oracle VirtualBox virtual NIC)
Service Info: Host: ETERNALROMANCE; OS: Linux; CPE: cpe:/o:linux:linux_kernel
{% endhighlight %}

`-sV` - Banner Grabbing<br/>
`-T4` - For fast scanning<br/>
`--script vuln` - Running full NSE scripts against our target<br/>
`-Pn` - I always throw a -Pn in there just in case the target blocks ping probes, although it's optional.<br/>


P.S From the beginning when I read that title of this vulnerable machine was EternalRomance I thought it would be easy peasy lemon squeezy with SMB Exploit,
but later after trying lot of exploits I found that it was named EternalRomance just because to trick us. (trick was successful in my case... lol I wasted lot of time on that)

Open webservice port: 80

I run the `Dirb` scan for enumerating directories
<br/>
`root@kali:~# dirb http://192.168.88.236/`
<br/>
{% highlight text %}
---- Scanning URL: http://192.168.88.236/ ----
==> DIRECTORY: http://192.168.88.236/images/                                   
+ http://192.168.88.236/index.html (CODE:200|SIZE:288)                         
+ http://192.168.88.236/server-status (CODE:403|SIZE:294)
{% endhighlight %}
<br/>
I've found nothing suspicious here, so I decided to visit webpage to see if there was any kind of hints, I've checked the source code code of index.html and there I've found text which was encrypted via<br/> Base64 encryption.<br/>
![httpauth]({{ "/images/indexpage_source_code.png" | absolute_url }})
<br/>
(for base64 decryption I'm using: https://www.base64decode.org/)
I decrypted this Base64 encryption and got this result = `/cybercube2018-eternalromance`
It was obvious that this was hidden directory so I entered http://192.168.88.236/cybercube2018-eternalromance

Aaand voila! We've got some kind of admin login page here!<br/>
Whenever I see such a login page,first thing I do is Blind SQL Injection: `'OR' 1=1`
![httpauth]({{ "/images/adminloginpage.png" | absolute_url }})

We've got the "Login Incorrect" message, but hell no! I won't buy that fairy tales^^ <br/>
Again I've checked source and I see code with this logic: if you would input the given POST parameters (username & password) some kind of image with name `samba.png` would appear.<br>
When I've done this, now I only got thi samba image and nothing to grip on, as I already mentioned I've tried lot of samba exploits and none of them worked. (no, I lie, actually they worked but no session was created in meterpreter)

So I've thought why not to try enumerating SMB folders.

`root@kali:~# smbclient -L 192.168.88.236` 
<br/>
![httpauth]({{ "/images/smb_folder_list.png" | absolute_url }})
<br/>

There we see cube2018 folder.

Now we need to access this folder, we run the following command in our terminal:
<br/>
`root@kali:~# smbclient \\\\192.168.88.236\\cube2018` <br/>
`ls`
<br/>
{% highlight text %}
  .                                   D        0  Tue Apr 10 08:29:04 2018
  ..                                  D        0  Mon Apr 23 02:10:56 2018
  Haha.txt                            N       61  Tue Apr 10 04:40:09 2018
{% endhighlight %}

Now let's exit from smb process with `Ctrl + C` and run the following command in terminal to download Haha.txt from our SMB folder.
<br/>
`smbget smb://192.168.88.236/cube2018/Haha.txt`

We've got this txt file, now let's see what's inside it!
<br/>
`cat Haha.txt`
<br/>
And again we've got text which is encrypted via Base64 encryption 
`bmFtZTogZXRlcm5hbHJvbWFuY2UsIHByb3RvY29sOiBkbnMoNTMvdGNwKQo=`
<br/>
Decrypting this text gave us result: 
`name: eternalromance, protocol: dns(53/tcp)

After few minutes of thinking and googling most common 53 port issues, I realised that it was<br/>`DNS Zone Transfer Attack`

so I instantly opened-up terminal and run following command: 
<br/> <br/>
`dig axfr @192.168.88.236 eternalromance`
<br/>
![httpauth]({{ "/images/dig_zone.png" | absolute_url }})
<br/>
Feels nice right? here we got all the information we need for editing our host file, so what are we waiting for? <br/>
`nano /etc/hosts` 
<br/>
Add new line in hosts file 
<br/>
`192.168.88.236 cybercube2018.eternalromance.com` <br/>
It should look like this when you'll run the following command<br/> `cat /etc/hosts`
<br/>
![httpauth]({{ "/images/cat_hosts.png" | absolute_url }})
<br/>
Now open up your browser and browse http://cybercube2018.eternalromance.com/
You'll see login page,as I already told u when I see login page first I run SQL Injection: `'OR' 1=1`
<br/>
![httpauth]({{ "/images/cat_hosts.png" | absolute_url }})
<br/>
It works! 
Now we see only fuckin empty textbox? WTF?<br/>
<br/>
![httpauth]({{ "/images/rce_textbox.png" | absolute_url }})
<br/>
(if you thought this then quit pentesting right now please, it's a RCE my friend)
<br/>
<br/>
So we've got RCE right now,which means that we can execute any command we want. <br/>
After running command `ls` in terminal, we see that we've got 3 files over there, those files are: index.php rce.php test.php<br/>
<br/>
![httpauth]({{ "/images/ls.png" | absolute_url }})
<br/>
Just browse this link through your browser http://cybercube2018.eternalromance.com/test.php and you'll be able to see text "aaa lasha" instead of real flag.txt of this machine. (i'm an asshole sorry for such weird ending lmao xD... but pentest is done!)<br/>
<br/>
![httpauth]({{ "/images/lasha.png" | absolute_url }})
<br/>
P.S this should intrigue you right? Who the hek is Lasha? Lasha Takashvili is my friend, cool guy, author of this vulnerable machine and the guy who inspired me to make this security blog.



 

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
