---
layout: post
title:  "[Vulnhub] Kioptrix: Level 1.1 (#2) Walkthrough"
date:   2018-05-09 15:07:13 +0400
categories: jekyll update
---
Hello fellas, today we'll walkthrough [Kioptrix](https://www.vulnhub.com/?q=kioptrix&sort=date-asc&type=vm) vulnerable machine. <br>
I will try to explain every step simple & short. <br>
<br>
Before I'll start pentest, I'll cover virtual machine common installation problems: <br>
 - Kernel panic - not syncing - Attempted to kill init!<br>
 - Virtual machine is not listed in netdiscover<br>
 
1. We can deal with kernel panic error with following solution:
Go to the VM Settings --> Storage delete CentOs4.5.vmdk from Controller: SATA and add it again in new hard drive in Controller: IDE

2. If virtual machine is not listed in netdiscover:
Go to VM settings --> Network and change from NAT to Bridged Adapter



Okay let's start our pentest now, as I already mentioned I'll explain every step simple & short. <br>
First things first, we need to scan our network to find out what's the IP address of our Kioptrix virtual machine.

'netdiscover -i eth0'
![httpauth]({{ "/images/kioptrix2_netdiscover.png" | absolute_url }})

 
{% highlight text %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}


[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
