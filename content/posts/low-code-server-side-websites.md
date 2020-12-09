---
title: Low Code Server Side Websites
date: 2020-12-09
---

# Low Code Server Side Websites

One overarching goal of mine ever since I got into this web development game > 10 years ago after graduating as an electrical engineer was to make making websites the easiest it could possibly be. I started with rails, and that isn't the easiest place to start if you don't already understand:

- sql
- html
- css
- javascript
- html forms
- http
- background jobs
- websockets

and how each of those parts fit together to make a great website or I guess you could call them web apps.

There's a lot of room for improvement in the number of keystrokes to deployed website metric there. Rails tries to help by generating a bunch of text, but the sheer amount of text you need to get something done is still a barrier to entry for most individual website makers, or you could shorten that to indie web makers or even indie makers.

So for the past I don't know how many years I've been studying up on languages and possible technologies that I could use to make websites with the least amount of keystrokes. One possibility was clojure and I dove into that face first. It did work, I noticed a substantial reduction in number of keystrokes, but there was still this strange formalism that I couldn't shake, a duplication of namespaces repeated across every file, and believe you me, there were a *lot* of files. I learned a few things from those clojure years:

- sqlite is enough
- the jvm is more trouble than it's worth for small to medium websites (the 99%, of which I am one of)
- clojure itself felt formal and rigid even with macros
- single file websites are the holy grail

One very productive person (tm) who you have probably heard of, [Pieter Levels](https://levels.io), also honed in on these aspects of web development, sqlite + single file. He uses php 7 which is interesting, but I never learned php, so not sure it would help me here.

One "web framework" that has always been interesting to me is [sinatra](sinatrarb.com). So I remade it in [janet](https://janet-lang.org)

I'm currently calling it [osprey](https://github.com/swlkr/osprey).

It's still really early days, and things will change, but [let me know what you think](https://github.com/swlkr/osprey/discussions)!
