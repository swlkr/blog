---
title: AlpineJS
date: 2020-04-08
---

# AlpineJS

Not only do I love the name, I love the concept. If you’re going to add interactivity to your website, you may as well do it in the html instead of rendering everything in javascript.

The concept behind alpine is similar to vue and angular but it takes a step back and reduces everything (at this time of writing anyway) to functions and data, alarm bells are going off because that’s the same thing as janet/clojure. No classes, just functions. Another neat property of alpine is that it’s small, < 8k min + gzipped. That’s a lot smaller than react and way smaller than even jquery.

There’s great examples on the readme but what I really want to stress is HTML; whether that comes from a CDN a la jamstack/static site or server rendered dynamically, HTML is the first class citizen here, alpine is second. Alpine fits into your html with data attributes, it doesn’t render html from javascript. It still has components but it really just needs data to start and it uses the html’s hierarchy to do its magic.

If you’re finding yourself refusing to include jquery in your next project and looking at one of the “you don’t need jquery” sites, alpine might be for you.
