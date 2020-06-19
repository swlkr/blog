---
title: JAMStack
date: 2020-03-04
---

# JAMStack

It’s been around for a while and I just figured it out, I’m an old, grumpy programmer now.

So, static sites right? I mean that’s fine, jekyll has been around forever. It’s a little more than that, but not much more, I can sum it up in one sentence:

*Do almost everything at build time.*

So let’s say you have something like [today in clojure](https://todayinclojure.com). It’s a list of links pulled in from various APIs. Normally, I would start a [VPS for $3.50/mo](https://www.vultr.com/?ref=7614094) (or because of clojure’s memory requirements $10/mo) and set up a cron job on that server to save API responses to a database and dynamically build the html on each request.

Alternatively, I could do all of that on a raspberry pi in my closet and commit the pre-built html to git and push that to a private github repo where netlify picks it up and shows the same pre-built HTML to everyone for $0/mo.

That’s exactly what I do now. There are some downsides to this approach, showing real-time analytics is harder, I’d have to serve an image or something from a server, which would cost money again, but were the real-time analytics that important to begin with? Not really. There was also an RSS feed which I wasn’t even sure was working, so I axed that too.

My opinion of JAMstack is high, but for dynamic stuff, or websites with lots of forms and an auth component, the [good old three tier arch](https://en.wikipedia.org/wiki/Multitier_architecture) is still the easiest thing around.

Of course there’s a whole other thing where I can talk about the diminishing need for those kinds of interactive websites anyway, thanks to phones, voice assistants and soon AR glasses, and instead an increasing need for headless APIs billed per request. Maybe next week.
