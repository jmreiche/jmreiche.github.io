---
title: Jekyll and Ruby issues on Mac
layout: post
date: 2022-12-10 06:46:04
categories: programming 
canonical_url:
---

It has been a while since I last updated this blog of mine. I have not been doing that much coding or technical fidling the last couple of months, apart from my day job as Solution Architect. 

I ofthen think about how I could write about some of the findings from my day job, but haven't really got around to it. Most of the findings are also more process/management/policy related than purely technical. But perhps people want to read about such things also?

But this page came to the recursive rescue of providing content itself from the action of me wanting to add content.

Sounds strange?

Allow me to explain.

I have two laptops at home. One is a Lenovo X1 Carbon Gen 9(X1C9) running Ubuntu 22.04, the other is my old 2014 Macbook Pro - which I am writing this on.

The X1C9 is my daily driver. Back when I bought it Apple had not brought the 14" MBP to market yet. I did not want the touchbar and also wanted to have an alternative to all the Apple devices. Hence the X1C9 with Ubuntu came to be. It is a brilliant setup where everything works. But every now and then I get a little bit tempted by the new M1 and M2 Apple laptops. 

This is where the old 2014 MBP comes into play. Because although I also use it as part of my photography flow, it is a good reminder/testbed for whether I want to return to macOS. 

Today I got a good serving of both pros and cons. 

First of all I really love the keyboard and trackpad of this MBP. It just feels so natural to type, browse etc. I must say that using the TrackPoint on the Lenovo is also a pleasant experience, as I can leave my fingers at the keys at all times. But the touchpad on the MBP is just better for browsing, using gestures etc.

But trying to run the code, for this static page, locally on the MacBook, also showed some of the potential niggles. If I remember correctly, I do not think that I ever got it to run locally on the MBP and just ended up using the X1C9 for it instead. Or perhaps I just took a brave-pill, went crazy cowboy and testet in production like a real hero.

This blog is based on the static page generator Jekyll which uses Ruby. Ruby is installed on Mac's as part of the OS, so you could come to the false conclusion that it would be easy to run it locally - but no...

When I tried to run "bundle install" I got some issues related to write access for the Ruby directory. Running "gem cleanup" as some people suggested, did not fix it. 

With some more research I found that it is suggested to use an environment manager for Ruby development on Mac. That makes sense.

The recommended one I found was chruby and ruby-install. Trying to install them from Homebrew worked fine, but then when I tried to install ruby with ruby-install it failed on a lot of checks. Looking for these online it felt like I was the first one in history with this issue.

At this point I almost gave up. 

But decided that my last resort would be to try another environment manager. I decided to try rbenv. The installation worked fine, but trying to install Ruby then gave this error:

> xcrun: error: active developer path ("/Applications/Xcode.app/Contents/Developer") does not exist

This let me to this StackOverflow [page](https://stackoverflow.com/questions/35009531/xcrun-error-active-developer-path-applications-xcode-app-contents-developer) where it was recommended to reset xcode-select by using this command:

> sudo xcode-select --reset

This history of my computer matched the explanations given in the SO thread as I had also removed Xcode from my machine.

Running that command seemed to solve all my issues, as I was able to install Ruby from rbenv, run "bundle install" and finally "bundle exec jekyll serve" to run this blog locally on my mac. 

Looking back, I wonder how many of my original issues was caused by the "xcode-select" issue. Perhaps that was just the issue all along?

But I got to spend some time doing technical work on the Mac, hence producing content for the blog that I had neglected - so all in all a success.
