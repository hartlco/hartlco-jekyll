---
author: Martin Hartl
format: post
layout: post
title: "Using Swift in Objective-C projects and Apps"
date: 2014-09-05
---

I started playing with Swift the day it was released at this years WWDC keynote. I created dozens of playgrounds and example projects and Apps exclusively using Swift. But now I finally started using Swift in [Mentio](http://mentioapp.com). I plan on writing every new class in Swift, thanks to the way the language is implemented, it's absolutely no problem to use Swift classes/methods in Objective-C and vice versa.

The most "challenging" part is to make sure Xcode created a bridging header, in which you import all your Objective-C files you want to use in Swift.
On the Objective-C side, import the Swift header, to make your Swift code available to the old and crufty pointer-code.
For example in Mentio:
```import "Mentio-Swift.h"```

That's it.

