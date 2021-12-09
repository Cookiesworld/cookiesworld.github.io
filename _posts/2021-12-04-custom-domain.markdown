---
layout: post
title: "Adding a custom domain to my portfolio"
date: 2021-12-04 11:40:55 +0000
categories: personal
---

I have held the domain cookieswork.co.uk for years without doing much with it so I decided it would be good to setup a custom domain for these github pages. I started by following the steps in [githubs documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-a-subdomain)

After loging into my domain provider I edited the DNS settings to create a CNAME forwarding to cookiesworld.github.io. A CNAME is a canonical name, which is a fancy way of saying its an alias for another address.

I then went to the pages section of settings for the repository and entered the custom forward. Github does a lookup check which trakes a short while then after a couple of refreshes my pages my site moved to the [https://blog.cookiesworld.co.uk](https://blog.cookiesworld.co.uk)
