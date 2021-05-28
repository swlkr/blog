---
title: I wrote a bare-bones, free, open source CRM
date: 2021-04-20
---

# I wrote a bare-bones, free, open source CRM

TL;DR I wrote a CRUD-tastic "CRM" web app to learn roda, sequel and turbo

[Check it out here](https://crm.isatiny.app)
[Check out the source here](https://github.com/swlkr/tinycrm)

It’s free to use, in fact it’s so free that you don’t even have to sign in to use it, just go to tinycrm.swlkr.com and BAM there it is.

How can it be free? What’s the catch? Well the catch is that very few people (if anyone) will use it, so there’s no point to try to charge money if no one is going to pay me. If you take that further, logically, there’s not even a point to make people sign up if no one is even going to use it!

So when you go to the website, it’s free to use, you can invite your teammates and start keeping track of deals right away without even so much as giving away your email!

## What’s the catch, really?

It runs on a $5/mo VPS alongside my other projects on a SQLite database so as long as I can afford $5/mo and the $8/yr to renew the domain (notice it’s on a subdomain) I’ll keep on hosting it for free!

## Tradeoffs

There are some trade offs. There’s no support, for one. For two, I have plans to add new features like csv import/export and a bunch of little things, but these may not ever happen. With something as free as this, there are no guarantees, so input your deals at your own risk.

I will say though, if someone out there finds this thing useful as is and you think it’s worth paying for, we can work out a deal and I’ll offer support, new features, whatever. I’ll even slap a price tag on it for new users and move you to a separate private instance. So it's not that risky, you’ll just have to pay for support and new features, like any other CRM.

This was really an exercise for me to learn

- roda
- sequel
- turbo

and... mission accomplished!
