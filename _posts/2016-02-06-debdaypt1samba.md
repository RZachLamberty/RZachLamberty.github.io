---
layout: post
title: It takes two to Samba
excerpt: "Love hating everything? Debian may just be for you! (samba edition)"
keywords: "debian, open, source, linux, hack, samba, file, system, shared, network, fstab"
series: debian
part: 1
---

Recently I've been toying around with the idea of taking the [coursera scala course](https://www.coursera.org/course/progfun), and naturally this turned into an existential analysis of how best, exactly, to set up my home network file system. You see, I intend to use an IDE (probably intelliJ just to piss of the eclipse holy warriors -- black and orange and blue color schemes are better, guys), and X-window forwarding that sucker would make me want to die 1,000 deaths. So I would prefer to edit the files *LOCALLY*, on my windows laptop, but execute them remotely on my linux desktop dev server... I grieve. This had the added bonus that if I'm going to go through the trouble of doing this for my single windows-to-linux connection, I might as well also do it for my linux-to-linux connection (I have two debian desktops).

Of course, that simple task ended up consuming a solid three hours of my Saturday. It started out simple enough; just take the directory I want to share (`/home/username/code`) and turn it into a samba share by updating my `/etc/samba/smb.conf` to include

{% highlight ini %}
[code]
comment = Shared code repositories
path = /home/username/code
browsable = yes
guest ok = yes
force group = username
writable = yes
create mask = 0664
force directory mode = 0775
{% endhighlight %}

and mounting (in the linux world) of the share in my `/etc/fstab` file *a la*

{% highlight shell %}
//my.ip.add.ress/code /mnt/code cifs uid=username,credentials=/home/username/.smbfsrc,rw 0 0
{% endhighlight %}

Of course, those are the lines that *work*. The lines I started with were missing the last three of the `smb.conf` file as well as the `uid` and `rw` in the `fstab` file. If you don't have those things, linux does this super amazing thing where it will let you mount the file system as user `nobody` in group `nogroup`, *create* files and directories in the mounted directory, but not actually have write access to anything there. Super fun! Can I adequately communicate to you the joy of the following:

{% highlight shell %}
$> touch test.txt
$> ls -alh
drwxrwxr-x 12 username username    0 Feb  6 14:57 ./
drwxr-xr-x  3 username username 4.0K Feb  6 13:50 ../
-rw-r--r--  4 nobody   nogroup     0 Feb  6 14:55 test.txt
{% endhighlight %}

I see that I cannot.

In any case, it's done now, and I have effectively spent up all of the time I had intended to spend on scala soo....... better luck next time, me.
