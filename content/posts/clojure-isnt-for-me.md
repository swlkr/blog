---
title: Clojure isnt for me
date: 2020-03-11
---

# Clojure isn't for me

I’m writing here today to tell you about my five year long obsession with clojure and why I recently quit obsessing over it.

The truth is, I knew it was never for me.

- I wasn’t a java programmer before I found clojure, I was a ruby (on rails) programmer.
- I don't write enterprise web apps as my side projects
- I don't need web scale resource hungry web server architectures

 To the people who have never programmed in either ecosystem, they look pretty similar, but the reality of working with each of them daily is a lot more nuanced than that.

Ruby and its gems hail from C. Need a speed up in a gem? Write a C “extension” and call that code from ruby. You start to see this everywhere when you really get into it, gems like nokogiri and puma are two notable examples, but there are plenty more. The ruby standard library takes advantage of the vast amount of C that was written before it.

Clojure on the other hand runs on the JVM and as a consequence relies on quite a few java idioms, maven and jars. On the surface it probably shouldn’t matter since everything so far seems analogous, but the real turning point for me was when I realized my whole career had taken place in either microsoft C++ land or unix C land, not java land.

Java to a C person is very foreign, and most of the code seems arbitrary, along with the JVM’s complexity. Things are better now with graal and native-image but politically speaking, oracle doesn’t have the greatest track record with open source software. C on the other hand has been open source friendly for quite some time. I know in the 70s there were some proprietary unix OSes but since the rise of linux, things have been great. There are multiple companies all using the open source linux kernel and linus torvalds is the longest running, most respectable (and disposable) BDFL across any open source project, ever. He makes enough money doing whatever he does and doesn’t feel like he needed to lock down the linux kernel and charge money for it.

So when you fast forward to 2020, the code running atop C compiles natively (with some flags and compiler ifdefs) to multiple platforms, ARM, x86-64 and windows and sure it’s not simple by any stretch of the imagination but from an application developers standpoint, the compiled code runs with less memory, faster and with less tooling than anything in Java land even compared to graal and native-image.

So the question I kept asking myself was why do I put up with java, graal and native-image and the tenuous open source nature they bring with them, when I can just use C and all of the glorious free software (that will always be free) that comes along with it?

That's when I found [janet](https://janet-lang.org) and I could finally combine my C experience with my Clojure experience.