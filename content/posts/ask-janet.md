---
title: Ask Janet
date: 2020-05-07
---

# Ask Janet

The question in a world of abundance is "why?". What is the point of making yet another website?

Well, I don't have 1500 reputation on stackoverflow, so I can't make a janet tag, and I would also like to stop searching through gitter for questions/answers. I feel like those are perfectly reasonable reasons to make a website.

With that, I set out to use my ✨shiny ✨new web framework [joy](https://joyframework.com) to recreate some of the bits of SO, but specifically for janet.

The first thing I think that's really cool about joy and about the stack in general is the low memory usage: 11280 kb on a raspberry pi. No clouds here, just tiny computers serving the internet.

I mean sure a blank joy app only uses 5 MB, but 12 MB is nothing to sneeze at, and this is with some pretty FAT sqlite queries, loading quite a bit of data into memory. Not to mention, CSRF protection, full blown encrypted cookies w/ sessions, I mean if you can imagine a web framework feature, this thing has it. So, now that I can run websites off of my raspberry pi, which is $35 and not a $5/mo VPS, what's next?

The stack still needs a little tweaking, for starters, it's ugly. There's no doubt about it, [water.css](https://www.cssbed.com/water.css-dark/) is great for fast prototyping with a dark mode, but it comes at the expense of a few rough edges. I'm looking toward [mvp.css](https://andybrewer.github.io/mvp/) for my next project.

The rest of the stuff can definitely stay though, I love [alpine.js](https://github.com/alpinejs/alpine), it's the perfect "framework" for already server rendered html. I'm also going to bring in turbolinks, assuming I can get rid of any flashes of unstyled content.

The stack is here to stay, I'm super happy with how fast things come together:

- [janet](https://janet-lang.org)
- [joy](https://joyframework.com)
- [alpine.js](https://github.com/alpinejs/alpine)
- [water.css](https://www.cssbed.com/water.css-dark/) -> [mvp.css](https://andybrewer.github.io/mvp/)
- [pylon.css](https://almonk.github.io/pylon/)
- [ubuntu](https://ubuntu.com/blog/ubuntu-20-04-lts-arrives) -> [alpine](https://alpinelinux.org)
- [raspberry pi 3 model b+](https://www.raspberrypi.org/products/raspberry-pi-3-model-b-plus/)

[Here's the source](https://github.com/swlkr/askjanet)

[This is my stack. there are many like it, but this one is mine.](https://en.wikipedia.org/wiki/Rifleman%27s_Creed)
