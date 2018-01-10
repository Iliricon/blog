---
title: "New year, new server, new blog"
date: 2018-01-09
tags: ["musing", "hugo"]
description: In Germany, we have a saying "Aller guten Dinge sind drei" ("Of all good things, there are three"). So here are my three good things for the new year. I have a new server, a new blog and i started work on my thesis!
draft: false
---

## A new year

Well, everybody probably realized by now that we have a new year :D

All in all, the year started pretty lousy, we got sick and lay in bed for a week, but we also finally got around to getting our own server and hosting a pretty little new website.

## A new server

I've always wanted to have my own server, but since I hate teaching myself stuff from scratch and feeling stupid, I never got around to actually getting one. Now my boyfriend, who is a master with all things technological, convinced me to rent a server and to start a little shared admin project. We are now running both our websites on a small VM hosted by serverprofi24.

We decided to run the whole installation of an ansible script, which I found pretty intuitive to work with. Ansible is a tool that automates deployment for servers. You write files with all the instructions to run on the server and a templating engine for config files. You can check it out at [ansible.com](https://www.ansible.com/). But the majority of that work was done by my boyfriend, so sadly I can't claim a lot of credit.

I registered a new domain (cvoelcker.de) and started to move my old domains to point to this webpage. Hopefully, I will have a complete and concise webpresence soon. The domains [cvoelcker.net](https://cvoelcker.net), [cvoelcker.de](https://cvoelcker.de), [claas.space](https://claas.space), and [iliricon.de](https://iliricon.de) will hopefully soon all point to this site, or another useful webpage on this server.

## A new website

I love this theme! Even though I hate to admit it, my boyfriend convinced me (again) that he has already found a perfect solution or software for the thing I want to do. So while I was out looking for a nice CMS or static page generator, my boyfriend had already set up a blog with [hugo](https://gohugo.io). Hugo is a static page framework which builds websites from markdown files. It is very easy to setup and use and it has a metric ton of nice [themes](https://themes.gohugo.io/) to choose from. I'm using a very plain, minimalistic theme, as you have probably noticed.

Since some friends already found mistakes in the template, my boyfriend convinced me to try and fix them myself. And I did it! I even commited my changes to GitHub and had my first publicly accepted pull request merged. I'm pretty proud of that. This framework and the fact that it leverages the power of HTML5 and CSS to get rid of all JS bullshit really got me exited to work on webpages again. Publishing can be so much fun when you don't have a slow loading CMS, no JavaScript hassle, and a deploy script of one line which runs in a quick second.

All in all I can say, I'm pretty happy with my new blog. Have a happy new year!
