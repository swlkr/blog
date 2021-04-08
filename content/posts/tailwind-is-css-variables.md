---
title: Tailwind is just css variables
date: 2021-04-08
---

# Tailwind is just css variables

My argument against tailwind and all atomic css is that it can be reduced even further to just using regular css and css variables. Yes you will have to use a separate file instead of your template or html file, but isn’t that what split panes are for?

I still think there is a place for atomic classes, mostly for margin/padding and things like that, but for the most part you should probably just use grid and flexbox for layout and then everything else should be a css variable. A good rule of thumb is to make sure you never have a hardcoded number in your css, always reference a variable. This keeps your design consistent.

The key to consistency isn’t using a framework it’s choosing

- spacing scale (padding/margin)
- shadows
- borders
- colors
- font scale

ahead of time and assigning them to css variables, and only referencing those variables.

I’ve found I can move even faster without tailwind by just copying most of their css variables and writing a mix of classless css, atomic css, flexbox and grid. You should try it!


