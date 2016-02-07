---
layout: post
title: Guilty as Cinnamon
excerpt: "Love hating everything? Debian may just be for you! (cinnamon edition)"
keywords: "debian, open, source, linux, hack, cinnamon, drivers, adm, glx, fglx, apt, get, install, murder, black, screen, cry"
series: debian
part: 2
---

Ever since my wife and I moved to DC we've been waiting on my computer desk, and it finally arrived earlier today. Naturally, I decided to skip all the other fun things I could be doing today and put it together, set up my computer workstation (complete with [this big bad mammajamma](http://www.dell.com/ed/business/p/dell-u3011/pd)) and play a little hack-y-sack. As any good debian user would I plugged in some subset of the required cords, fired up my workstation, and logged in. That's when I noticed this lovely little note:

<img src="http://www.thehyperlink.net/sites/thehyperlink.net/files/debian-8-or-mint-cinnamon-software-rendering-mode.png">

If you click on that image, it simply goes away, never to be heard from again.

Obviously, this was an offense against my deepest self. No matter what happened today, that god damned message would *not* be greating me on login any more. I google-foo'd the bejeezus out of this one, and came to a simple conclusion: it was time to install a better GL driver. [This one](https://wiki.debian.org/ATIProprietary), in fact. I was `apt-get`ting like a champ before I read this little nugget of knowledge:

> This driver is incompatible with the GNOME desktop, as it does not support the EGL interface. It is recommended to use the free radeon driver instead.

Naturally, I finished the install and configuration of, oh, I don't know, 25 libraries, and then popped on over to the radeon driver download page and performed the install there. I autoremoved the `libfglrx` library an erythang. So imagine my surprise, after so carefully mastering that precarious sequence of events, when I logged in to my desktop after restart and experience a plain black screen. If you are imagining "no surprise" or "swearing at himself as he walked to the fridge to grab a beer," your imagination is well-grounded in reality and perhaps you should expand your concept of possibility.

In the end, my saving grace was (as always) some ungodly debian troll forty thousand posts beneath the sea who pointed out that one must

{% highlight shell %}
update-alternatives --config glx
{% endhighlight %}

Now my gears are spinning at >= 0 FPS. Bully for me.
