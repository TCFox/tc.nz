---
layout: post
title:  "Building tc.nz - Part 1"
date:   2017-06-13 19:54:06 +1200
tags: jekyll azure web-development
excerpt_separator: "<!--more-->"
---

After my first utterly failed attempt at trying to build a website for myself based off an [HTML5 Up!](https://html5up.net/) template and some half-arsed hosting on a Windows Server box that promptly fell over, this domain spent well over a year helpfully [displaying advertising for Internet Information Services](https://web-beta.archive.org/web/20161009043440/tc.nz) instead of actually being useful. Which is a shame, seeing as it is such an epic domain name!

So, I finally got off my arse and set up this website. I wanted to do something a bit different, something that was low maintenance to both maintain and update with content. My recent fascination with Markdown led me to [Jekyll](https://jekyllrb.com/), a Ruby-based static content generator used extensively by [GitHub](https://github.com/) to support their [GitHub Pages](https://pages.github.com/) feature.

So, what better way to kick off this (hopefully more stable!) iteration of my new site than to share with you how I built it?

<!--more-->

## What I wanted

As a security-concious fox (my day job is working as a Security Engineer for a prominent journalism company in New Zealand), I wanted to find something that was uniquely secure from security vulnerabilities, and once built, did not require the same level of constant upkeep and cuddling as... well, another popular blogging platform that shall go unnamed. I quite seriously looked at [ghost.io](https://ghost.io), but this still required maintaining a virtual machine (or two), a database, security updates, etc, or paying for their subscription SaaS offering (which would likely have constrained me in what I would be allowed to do with my blog a bit).

I also wanted something that would be simple to update. I didn't want to dick around with FTP/SSH/etc just to update my blog, I wanted it to be a straightforward process which I could do from anywhere (even my phone!)

Lastly, I wanted the platform of choice to be flexible, and not contrain me into having to make tradeoffs around what I wanted to do just so that the platform would stay happy.

## Introducing Jekyll

I quickly learned that there was a "best of both worlds" between old-school "handcrafted HTML/CSS/JS, updated manually" and traditional "server-side rendering with admin panels and a database" blogging. Through the use of platforms like Jekyll, Octopress and downr, you can write simple markdown for your posts, place it amongst a bunch of template files and run a simple command to have all of your HTML files build right there for you at "compile-time", ready to be uploaded to a simple static hosting service with no need to involve PHP, ASP.NET Core, Ruby, NodeJS, Python or any other server-side language at runtime. I ended up choosing Jekyll as my blog platform of choice (even despite downr being entirely based on ASP.NET Core, my server-side language of choice!) purely due to GitHub Pages *also* using Jekyll behind the scenes, which gives it a rather sizable community to support the platform.

Deployment was also made easier through a simple-to-use pipeline. All of my Jekyll code, including my posts and (most of) my assets are checked out from [my repository on GitHub](https://github.com/TCFox/tc.nz) and edited using [Visual Studio Code](https://code.visualstudio.com) (which has an excellent markdown editor, by the way!). Once I'm done, I simple commit the changes back to GitHub, and some cloudy magic automatically slurps up the changes, regenerates my site, and deploys it into a VM that is managed entirely for me.

## Introducing Azure App Service:

What's this "cloudy magic" you refer to? It's Azure App Service, an offering by Microsoft which gives me a set of VMs which are managed, balanced, updated, deployed to and monitored entirely for me, right out of the box. I simply point the App Service at my GitHub repo, it automatically checks out the latest version the moment there's a new commit, runs a build script, and within minutes a brandspanking new version of the site is live and serving traffic. It's even served over a CDN, which makes requesting content *so much faster* for my international readers!

I'll go into detail with all these pieces, and so much more, in the following few days!

---

Other posts in this series:

* _Coming soon! Stay tuned!_