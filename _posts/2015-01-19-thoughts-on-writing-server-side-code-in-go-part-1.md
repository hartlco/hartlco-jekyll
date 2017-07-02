---
author: Martin Hartl
format: post
layout: post
title: Thoughts on writing server side code in Go Part 1
date: 2015-01-19
---

As I have mentioned in a [previous post](http://martinhartl.org/2015/), I want to learn a new programming language for backend development this year.
I’ve written a few Sinatra applications and played with Node.js and Express in the last years but I would never consider myself a backend developer. So take all my upcoming statements with a grain of salt, I just don't have enough experience in this field.

Running the actual Ruby or Node App always was a hassle for me. There are many steps that could go wrong. Do I have the correct version set up in the version manager, is the package manager set up correctly, are packages installed for the right user in the right place, is the production machine set up similar to the development machine? To be honest, I was never sure, if I fully understood all the steps I had to do in order to run the App.

The process of deploying my first App written in Go was slightly different:

`go build testserver`

`./testserver`  

I just compiled the code into a binary and ran it. Without any setup, I could copy the binary to a different machine (with the same architecture and platform, or cross-compile at the beginning) and run it there. There's definitely downsides to compiled server side code, but I'm really happy with it and it completely fit's my current development style.

