---
title: The making of ridge.css
date: 2020-05-28
---

# The making of ridge.css

As with most things I do at the computer, this was born out of frustration with existing tooling.

If I had to order my fustrations, they would roughly fall in this order:

- rails
- bootstrap
- tachyons / tailwind / atomic css
- react.js
- multi-file web app development

I've been tackling these one by one, by either switching to something else that already exists or making my own:

- rails -> joy (still has a long way to go)
- bootstrap -> tachyons.css
- react.js -> alpine.js
- tachyons / tailwind / atomic css -> ridge.css (what this post is about)
- multi-file web app development -> (coming soon)

The switch from bootstrap to tacyhons or I guess tailwind for the rest of the industry was pretty obvious to me. I'm surprised it took so long for the industry to come around to atomic css. The benefits when paired with components are incredible, you can move way faster, make your designs really stand out as opposed to with bootstrap, hope there's a theme or something that you don't want to change at all.

Don't get me wrong, I like themes, but I don't like it when to override one little style you have to deep dive into the internals of bootstrap's insanely complex scss files which used to be .less and even more obscure. Lately I've completed 1.0's of two somewhat simple projects, [JanetDocs](https://janetdocs.com) and [AskJanet](https://askjanet.xyz). The one thing I've learned from this is that if I don't have my css/design work outsourced in the form of classless css (or mostly classless css), my odds of completing a project are super low. I started several projects with tachyons in the past and I was struggling under the weight of endlessly tweaking classes and trying to get padding/margin just right. Not to mention the lack of bootstrap grids had me floundering when it came time for responsive stuff. I just want to recap:

- bootstrap isn't all bad, themes are nice, grids are great
- atomic css isn't all bad, customization is incredible for designs that stand out

Enter [classless css](https://github.com/dbohdan/classless-css), which is really going back to the heart of website making from the 90s, with a twist. Instead of defining classes, you just use the html5 elements as they are and they are styled to look not ugly. So you basically get a stripped down version of bootstrap with the themes but no grid. No grid? No problem. That is where [pylon.css](https://almonk.github.io/pylon/) comes in. It's essentially swift ui for the web, which is great. What's funny is swift and native development in general never suffered from this culture of "mixing content with presentation". To hell with that, I want to make good looking websites fast, I don't care about dogma.

Alright, so this kind of sets the stage for what ridge.css is, it's a maximal frontend framework for making web apps fast, as in quickly not necessarily fast performing, I leave that up to the server ;). Here's the constituent parts of this "framework":

- alpine.js `x-` attributes for interactivity, just add alpine.js
- a set of classless themes (which currently there are only two, one light and one dark)
- a dash of atomic css classes
- a modified responsive version of pylon.css for page layout

With those four things, you can accomplish a lot without switching files and without declaring classes forever and ever in your html.

I'm currently still working on ridge.css getting it ready for general use, but it's coming along great. I'm going to make something relatively non-trivial and report back and probably release ridge then. And then write an ebook and sell it as the JARS stack, joy, alpine.js, ridge.css and sqlite.
