---
author: Martin Hartl
title: Posting on the go with Jekyll, Workflow and Git2Go
format: post
layout: post
date: 2016-10-04 13:19
---
Previously this site was run with a Wordpress installation. I wanted the option to post on the go using iOS Apps. The official Wordpress iOS App is just ok, but [Ulysses](https://ulyssesapp.com) is great for it. After a few months I was more and more annoyed with the obvious Wordpress drawbacks. I don't need a whole CMS with a MySQL database to just show a few cached html pages. That's why I switched to the static site generator [Jekyll](https://jekyllrb.com). It's basically a command line tool that transforms Markdown files into an HTML page. My Uberspace directly serves the file. This solved all my gripes I had with Wordpress but posting on the go was no longer possible. With a bit of automation and a few Apps I was able to solve this problem:

## Github
The git repository containing all the posts and files for Jekyll is hosted here on [Github](https://github.com/hartlco/hartlco-jekyll). After a change I could log into the server, pull the latest changes from Github and build the Jekyll site again. This screams for automation:
Github supports Webhooks, this means you can specify a URL that receives a POST request after a change to the repository, like receiving a new commit through a push.
I wrote a tiny Sinatra App that is listening for POST requests and triggers different shell scripts depending on the endpoint. The code for the App is available [here](https://github.com/hartlco/pan) and it's running on my Uberspace, waiting for the POST request from Github to trigger my deploy shell script. This script only pulls the latest changes and runs the Jekyll command to build the site again.
The same would work with Githooks on my Uberspace but this would require pushing to this repository directly. I wanted the Github repository to always have the latest changes and be the source of truth.

## Creating Markdown files with Ulysses/Drafts and Workflow
I'm using [Ulysses](https://ulyssesapp.com) to write longer posts and [Drafts](http://agiletortoise.com/drafts/) for short, tweet-like status posts. My Jekyll setup expects posts in the Markdown format with a bunch of metadata at the top of the file. To create those files on iOS I'm using the share sheet from inside the Apps to trigger different Workflows. These Workflows create the correct metadata, append the content and name the files accordingly. One Workflow example is [here](https://workflow.is/workflows/9a2d5a9e021d47b78c6ed817cbf7bd87).

## Git2Go
To actually add the file to my Jekyll repository I use the wonderful [Git2Go](http://git2go.com). I'm importing the file Workflow generated using the native iOS share sheet, commit it and push it to the Github repository. As described in the beginning, the site gets rebuilt and the new post is live.

This process has been very reliable for me, everything is under my control and super fast. 
By the way, I wrote and published this post while lying at the pool.
