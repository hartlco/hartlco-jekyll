---
author: Martin Hartl
format: link
layout: post
linked_list_url: http://www.raywenderlich.com/90974/swift-style-guide-december-2014-update
date: 2014-12-13
title: "Swift Style Guide: December 2014 Update"
---

This update to Ray Wenderlich's Swift Styleguide  tackles one conflict I repeatedly have while writing Swift code.

Naming variables and constants for the optional binding shortcut:

> For optional binding, we went for the simplest solution: to shadow the original name.

    var textLabel: UILabel?    
    // later...
    if let textLabel = textLabel {
      // do something with textLabel, which is now unwrapped
    }
    

Nice and pragmatic solution.

Declaring and implementing protocol compliance in an own class extension also is a really nice way to unclutter big classes.

>[...] we do suggest having each protocol conformance declared in its own extension:

    class MyViewController {
      // Standard view controller stuff here
    }
    
    extension MyViewController: UITableViewDelegate {
      // Table view delegate methods here
    }
    
    extension MyViewController: UITextFieldDelegate {
      // Text field delegate methods here
    }
    
    // etc.

 


