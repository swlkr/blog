---
title: Classless CSS
date: 2020-04-01
---

# Classless CSS

You’ve heard of [tailwind](https://tailwindcss.com) and maybe [tachyons](https://tachyons.io), functional css, or atomic css. Well the next step is classless css.

But Sean, what does that mean?!

I’ll tell you what it means.

It means, you just write html and that’s it, no classes, no inline styles, just html. It’s the way things were always meant to be™️.
We’ve come full circle. Or maybe it’s just me and my MVPs coming full circle.

Here’s a great example:

    <h1 class="f1">tinymailer</h1>

This just becomes:

    <h1>tinymailer</h1>

and it's styled correctly in any screen size, or styled well enough.

But what about grids you say, how can you make a grid without the ol' `row`/`col` combo?

    <div class="row">
      <div class="col-xs-6">
        I'm on the left
      </div>
      <div class="col-xs-6">
        I'm on the right
      </div>
    </div>

That's a mouthful, I have a little something for that as well, [pylon.css](https://almonk.github.io/pylon/):

    <hstack stretch>
      <div>I'm on the left</div>
      <div>I'm on the right</div>
    </hstack>

And with the tiny bit of css you get from pylon, you get flexbox grids instead of the rigid bootstrap 3 ones, with all the weird margin/padding nonsense that comes with them.

CSS Grid may make even this obsolete, though I still prefer to keep everything in the html.

Layout and styling have officially been solved for MVPs at this point and MVPs are really all that matter, right?

Here's a great link with some "themes" and things you can slap into your project today!

[https://www.cssbed.com](https://www.cssbed.com).

Don't write another css class, go classless!
