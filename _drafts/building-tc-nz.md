---
layout: post
title:  "Building tc.nz"
date:   2017-04-16 15:41:06 +1200
categories: jekyll azure web-development
excerpt_separator: "<!--more-->"
---

After my first utterly failed attempt at trying to build a website for myself based off an [HTML5 Up!](https://html5up.net/) template and some half-arsed hosting on a Windows Server box that promptly fell over, this domain spent well over a year helpfully [displaying advertising for Internet Information Services](https://web-beta.archive.org/web/20161009043440/tc.nz) instead of actually being useful. Which is a shame, seeing as it is such an epic domain name!

So, I finally got off my arse and set up this website. I wanted to do something a bit different, something that was low maintenance to both maintain and update with content. My recent fascination with Markdown led me to [Jekyll](https://jekyllrb.com/), a Ruby-based static content generator used extensively by [GitHub](https://github.com/) to support their [GitHub Pages](https://pages.github.com/) feature.

So, what better way to kick off this (hopefully more stable!) iteration of my new site than to share with you how I built it?

<!--more-->

## What I wanted

* Easy authoring
* Automatic publishing
* Minimal sysadmining required
* No server-side dynamic content
* Flexibility to add as much weird and wonderful security features to the site as I like

## The development environment

* GitHub
* Visual Studio Code
* Ruby on Windows (ugh)
* Jekyll
* GitKraken

## Hosting

* Azure App Service
* Azure DNS
* Azure CDN

## Publishing

* Kudu + GitHub
* Site version header

## Site features

### Security

* TLS + Let's Encrypt
* HTTP -> HTTPS redirection
* HSTS + Preloading
* CSP
* Expect-CT
* X-Frame-Options
* X-Xss-Protection
* X-Content-Type
* Referrer-Policy
* CORS
* Keybase Proof
* Commit signing
* Sub-resource Integrity
* Manual data signing (matrix.json)
* bundle-audit (in progress)

### Publishing

* Asset Pipeline + CDN support
    * SCSS/JS bundling
    * Image support
    * Large assets (video/audio/files/huge images) kept out of repo
* Writing posts + drafts
* ATOM (RSS) feeds
* SEO (in progress)
* HEAD meta tags
* Sitemap

### Theming

* Bootstrap
* Font Awesome
* Dynamic nav menu
* Recent posts

### Fun easter eggs

* X-Powered-By
* Kiwi Ipsum test page

### Other

* Error page handling
* Publishing matrix keys

## Issues I encountered

* Bootstrap Ruby Gem + Windows (encoding)


