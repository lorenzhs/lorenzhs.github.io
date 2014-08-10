---
layout: post
title: Glowing Bear — IRC for the 21st century
---

Glowing Bear is a modern IRC experience. I'm probably not alone in thinking that a console client, running in a terminal multiplexer on your server is not the best experience. While it's not too bad on the desktop, mobile usage is just a pain. And what if you have to use someone else's machine and want to access IRC? If they're using Windows, chances are, you'll have to download an ssh client first!

So we take that console client running in a terminal multiplexer on your server and trash it. Right? No. Writing a good IRC client is really hard, and why throw away all the good work someone else has done to provide us with a fast, stable, reliable basis for our work? Instead, we're going to use that console client and connect to it right from your browser!

That's where Glowing Bear comes in. It's a [WeeChat](http://www.weechat.org/) relay plugin, meaning that it connects to a running WeeChat instance (there's the console client running in a terminal multiplexer on your server again). WeeChat does all the heavy lifting for us. So what's left is showing the data we get from the server in a nice way, providing you with a slick IRC experience.

Glowing Bear uses AngularJS and Bootstrap to provide you with a responsive design that shows you exactly what you need, whatever device you're using. On a big screen, you'll have your buffer list on the left, the nicklist on the right, with the actual lines in the middle and an input bar at the bottom. When using a mobile phone, only the chat lines and the input bar remain, all the rest is hidden away until you need it. Time for some screenshots?

[![Glowing Bear in a desktop environment](/images/gb-desktop-small.png)](/images/gb-desktop.png)
Glowing Bear as it looks on the desktop, running in Chrome for Linux

[![Glowing Bear on a smartphone](/images/gb-mobile-small.png)](/images/gb-mobile.png)

On a mobile phone, the irrelevant stuff is gone until you need it and swipe it in from the sides.

If you're interested in using Glowing Bear, simply follow the usage instructions on the [landing page](https://glowing-bear.github.io/glowing-bear/) to set up the relay plugin. If you fancy encryption (which is generally a good idea), you'll find help on setting that up as well. And should you want to host Glowing Bear yourself, just clone the repository and serve it with a web server:

{% highlight bash %}
$ git clone https://github.com/glowing-bear/glowing-bear.git
$ cd glowing-bear
$ python -m SimpleHTTPServer
{% endhighlight %}

We'd love to hear your feedback! To use Glowing Bear, simply head over to [http://www.glowing-bear.org](http://www.glowing-bear.org). Join us at [#glowing-bear](irc://chat.freenode.net/#glowing-bear) on freenode! Check out our [project page](https://github.com/glowing-bear/glowing-bear) on GitHub to report issues or simply have a look at the source.

Update: We moved to a GitHub organisation, [glowing-bear](https://github.com/glowing-bear) and our own domain, [http://www.glowing-bear.org](http://www.glowing-bear.org), so I updated the links in the text accordingly.
