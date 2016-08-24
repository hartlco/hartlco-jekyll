---
title: Highlight (bold) First Line in a UITextView
date: 2015-05-21
format: post
layout: post
author: Martin Hartl
---

For a new App I'm working on, I wanted to use a single UITextView for entering a headline/caption and a body text. The first line (until a line break) should be treated as the headline, the rest as the body text. For a visual distinction, the header should be bold.

![][image-1]

This can be easily solved for static text using NSAttributedString:

```swift
override func viewDidLoad() {
	...
	self.highlightFirstLineInTextView(self.textView)
}
 
private func highlightFirstLineInTextView(textView: UITextView) {
	let textAsNSString = textView.text as NSString
	let lineBreakRange = textAsNSString.rangeOfString("\n")
	let newAttributedText = NSMutableAttributedString(attributedString: textView.attributedText)
	let boldRange: NSRange
	if lineBreakRange.location < textAsNSString.length {
		boldRange = NSRange(location: 0, length: lineBreakRange.location)
	} else {
		boldRange = NSRange(location: 0, length: textAsNSString.length)
	}
	
	newAttributedText.addAttribute(NSFontAttributeName, value: UIFont.preferredFontForTextStyle(UIFontTextStyleHeadline), range: boldRange)
	textView.attributedText = newAttributedText
}
```

I'm not using Swift Ranges because NSAttributedString-Objects do not play well with them. To get an NSRange from `rangeOfString` I'm bridging `text` to NSString.

All the nice highlights will get butchered when the user starts to enter or edit the text. To prevent this, we can adjust the `typingAttributes` of the UITextView depending on whether the headline or the body is changed:

```swift
let headerAttributes = [NSFontAttributeName : UIFont.preferredFontForTextStyle(UIFontTextStyleBody)]
let bodyAttributes = [NSFontAttributeName : UIFont.preferredFontForTextStyle(UIFontTextStyleHeadline)]
 
override func viewDidLoad() {
	self.textView.delegate = self
	...
}
 
// MARK: - UITextFieldDelegate
 
func textView(textView: UITextView, shouldChangeTextInRange range: NSRange, replacementText text: String) -> Bool {
	let textAsNSString = self.textView.text as NSString
	let replaced = textAsNSString.stringByReplacingCharactersInRange(range, withString: text) as NSString
	let boldRange = replaced.rangeOfString("\n")
	if boldRange.location <= range.location {
		self.textView.typingAttributes = self.headerAttributes
	} else {
		self.textView.typingAttributes = self.bodyAttributes
	}
	
	return true
}
```
	

This will apply the correct attributes to the changed header or body part of the text.
If someone has a better solution to this, please let me know!

[image-1]:	{{ site.url }}/assets/2015-05-21-Highlight-first-line-of-text.png