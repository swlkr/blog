---
title: NEW MAGIC
date: 2020-12-17
---

# NEW MAGIC

What is it?! What is the NEW MAGIC referenced in this tweet:

{{< tweet 1275901955995385856 >}}

Most people in the replies seem to think it's some kind of combination of stimulusjs over websockets?

Or maybe some kind of websocket-y reactive-ui thing that takes data from active record models and sends
them over websockets.

There's more hints in the thread below and in this [HN thread here](https://news.ycombinator.com/item?id=23643458)

But that's neither here nor there. The larger trend I think to extract from this and laravel's websocket thing and phoenix's websocket thing is that monolithic web frameworks aren't backing down from heavy server side computation. Oh no. They are doubling down on it.

This may be an equal and opposite reaction to the complexity that SPAs hath wrought.

Logically, it makes a lot of sense.

Currently, you make an http/1.1 connection (with keep alive), even if you're making an http/2 connection, that's just to caddy or nginx, the real proxied code underneath, probably apache is using http/1.1, and then that's it. The connection ends, you got your css, js, images, videos and html. You're good. Until you need to make another connection. Then it starts all over again.

This is kind of dumb. Servers are really powerful now, they can keep a ton of connections open, no problem. Especially in the case of elixir with the erlang VM. So you may as well take advantage of this new found power and reduce your complexity at the same time.

It turns out that HTML is alive and well and going forward, heavy client side frameworks with their battery sucking ways will go the way of the dinosaur.

Stateful monolithic web frameworks, who would have thought?
