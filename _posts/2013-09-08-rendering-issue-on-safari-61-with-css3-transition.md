---
layout: post
title: "Rendering issue on Safari 6.1 with CSS3 transition"
description: "This series of articles will tell you some interesting issue you may or may not know on modern web browsers."
category: Those interesting browser issues
tags: [CSS3, browser]
---
{% include JB/setup %}

Apple is expected to introduce new iPhone model on Tuesday (Sept 10th), which will carry a new version of mobile Safari browser. And just a few days ago (Sept 5th), I got an Apple Developer email to download their latest [Safari 6.1 Seed 7](https://developer.apple.com/downloads/index.action?name=Safari). Running [StubHub](https://m.stubhub.com) mobile web site on these, an interesting issue came out.

#### What's the issue?
[StubHub](https://m.stubhub.com) mobile web has a responsive navigation menu, when user accesses it on smartphone, the navigation menu will hide at left side of screen by default with a Hamburger button to toggle it. On iOS 6 and other webkit browser, the menu works well and can open/close smoothly by leveraging CSS3 transition and 3D acceleration. Recommend to read [this](http://blog.teamtreehouse.com/increase-your-sites-performance-with-hardware-accelerated-css) to learn more about CSS3 transition with hardward accleration.

When check it on iOS7 preview version and Safari 6.1, the navigation menu is gone! See below screenshot:

<image src="/assets/images/iOS7_navi_issue.png" width="320" height="568" />

#### The root cause
After some investigation, I found the cause of this issue under my current implementation:

	.prevFlash{
		-webkit-backface-visibility: hidden;
   		-moz-backface-visibility: hidden;
   		-ms-backface-visibility: hidden;
   		backface-visibility: hidden;
	}


Please visit this [demo page](http://cdpn.io/oInqL) for a simple and complete case. Scan below QR code to open in your mobile browser.

<image src="/assets/images/iOS7_transition_issue_demo1.png" width="160" height="160" />

From the demo page, there are two ways to fix this issue:
1. By removing `backface-visibility`. However, I added this CSS attribute for the purpose of removing a flickering effect when toggling the menu (PS. [this article](http://blog.teamtreehouse.com/increase-your-sites-performance-with-hardware-accelerated-css) mentioned about this.)
2. By adding `pespective` attribute to the `.prevFlash` declaration.


#### Another answer?!
When I was debuging this issue on iOS7 with Safari developer tool, I found an interesting phenomenon. In Safari developer tool, I tried to monitor _**Layout and Rendering**_ under _**Timelines**_ tab to look for any rendering clue, but then the menu worked well unexpectedly! Once I stopped recording, the issue came again. That made me guess in this way - with monitoring open, the browser might force the navigation menu to render. So I wonder what if I make the menu visible when page loading and hide menu after page loads? Check another [demo page](http://cdpn.io/iJncf) for this, or scan below QR code to open in your mobile browser. 

<image src="/assets/images/iOS7_transition_issue_demo2.png" width="160" height="160" />

#### Just my guess.
Both Chrome 20.9 and Safari 6.1 both use `-webkit-perspective:none;` as default value. Why it does not work on Safari 6.1? 

It probably because with this attribute, Safari will put the HTML elements into a 3D perspective, and the "out-of-screen" elements (on page load) might be placed at a place where none perspecitve value can't make the browser show them. Just like looking at a plane at a particular place, what you see is just a line.


---
#### About
[**&lt;Those interesting browser issues&gt;**](/categories.html#those%20interesting%20browser%20issues-ref) is a column for me to share those interesting bugs I encountered when I developed the [StubHub](https://m.stubhub.com) mobile web site. Each article will only tell about one issue - which is simple, rare and interesting.

---