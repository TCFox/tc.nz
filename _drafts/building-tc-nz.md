---
layout: post
title:  "Building tc.nz - Part 1"
# date:   2017-04-16 15:41:06 +1200
tags: jekyll azure web-development
excerpt_separator: "<!--more-->"
---

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
* $$$!
* Blog comments


