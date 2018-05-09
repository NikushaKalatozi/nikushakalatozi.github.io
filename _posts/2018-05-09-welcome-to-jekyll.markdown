---
layout: post
title:  "[Vulnhub] Kioptrix: Level 1.1 (#2) Walkthrough"
date:   2018-05-09 15:07:13 +0400
categories: jekyll update
---
Hello fellas, today we'll walkthrough [Kioptrix](https://www.vulnhub.com/?q=kioptrix&sort=date-asc&type=vm) vulnerable machine. <br>
I will try to explain every step simple & short. <br>
<br>
Virtual machine common installation problems:<br>
 - Kernel panic - not syncing - Attempted to kill init!<br>
 - Virtual machine is not listed in netdiscover<br>
 
1. We can deal with kernel panic error with following solution:
In the VM Settings delete CentOs4.5.vmdk from Controller: SATA and add it again in new hard drive in Controller: IDE

2. If virtual machine is not listed in netdiscover, go to VM settings --> Network and change from NAT to Bridged Adapter

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
