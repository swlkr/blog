---
title: Rust, Ruby and Complexity
date: 2022-05-27
---

# Rust, Ruby and Complexity

I’ve been thinking about software development trade offs for a little while now, mostly around the concrete question:

_How can I run a bunch of dynamic websites on a $5/mo VPS?_

My $5/mo VPS journey has taken me near and far, from clojure and janet to things like lua, ruby, and most recently, rust.

Over the past few years I’ve learned a thing or two about simplicity and conversely, complexity. Simplicity isn’t one thing, instead it’s a series of trade offs on where to move the complexity.

Here are two examples:

1. __ruby__, the programmers best friend, presents simplicity in the language, garbage collected, dynamically typed, interpreted, while moving the complexity to the runtime. This results in large memory usage and slow program execution, as in computation is slow, not startup time.

2. __rust__, the complexity is in the programming language, static typing, and manual-ish memory management. the runtime is simple (or at least simpler than ruby's). This results in low memory usage and fast program execution.

Like a lot of things in life, simplicity is actually many things: trade offs. It's a journey, not a destination.
