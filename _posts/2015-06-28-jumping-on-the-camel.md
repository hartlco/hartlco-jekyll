---
id: 178
title: Jumping on the Camel
date: 2015-06-28T16:01:40+00:00
author: hartlco
layout: post
guid: https://hartl.co/?p=178
permalink: /2015/06/28/jumping-on-the-camel/
---
Since the beginning almost a year ago, this blog was run on a self hosted [Ghost](https://ghost.org) instance. Ghost is extremely easy to setup up, fast and fun to use but over the time the project naturally grew quite a bit in its size, to the point where I can&#8217;t easily wrap my head around it. More and more this made me feel uncomfortable. I&#8217;m no full time JavaScript developer or server admin, so I never was happy about making small changes like own linkpost support or updating the blog cms itself. This wasn&#8217;t the situation I wanted to be in for a small side project like this blog.

I was looking for alternatives with the smallest possible complexity that have the following few characteristics:
  
&#8211; Written in a language I have used before
  
&#8211; Native support for linkposts
  
&#8211; Posting on the go

I quickly settled with [Casey Liss&#8217;s](http://www.caseyliss.com) [Camel](https://github.com/cliss/camel) &#x1f42a; project. I&#8217;ve already tried it a year ago, but I chose Ghost because I thought the existing community around it may be useful. Now my requirements have changed.

I already had my posts saved as markdown files, so all I had to do was converting the format to Camel&#8217;s requirements, converting my blog theme and starting the single JavaScript file on my VPS. Done.

Camel has not web interface for posting mobile, but Dropbox on the server makes it possible to use all the great mobile Markdown text editors for posting on the go. I plan to write a follow up post about my specific setup soon.

I&#8217;m happy that I made the switch, everything went really smooth and is running great!