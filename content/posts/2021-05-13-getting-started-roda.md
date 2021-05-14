---
title: Getting started with roda
date: 2021-05-13
---

# Getting started with roda

TL;DR I made this handy [roda-starter repo](https://github.com/swlkr/roda-starter) so you can make ruby websites that run faster and use less memory than rails!

I use docker in anger and in development so that's where I'll be starting:

Go ahead and install docker if you don't have it already:

[https://docs.docker.com/docker-for-mac/install/](https://docs.docker.com/docker-for-mac/install/)

Now you can use my ace Dockerfile and roda setup:

```sh
git clone https://github.com/swlkr/roda-starter ~/Projects/your_project
cd your_project
docker compose up # listening on http://localhost:9292
```

Head over to localhost:9292 and check it out!

You should be able to sign up, login and logout via email (magic link) auth.

There are no styles, so good luck trying to click those links.

Here's a quick list from the readme of some cool features:

- a locked down content security policy (everything served from same domain)
- cross site request forgery tokens
- asset compilation in production
- a docker and docker compose file
- a .env file for docker to set env variables
- [sequel](http://sequel.jeremyevans.net) with model plugin
- [markaby](https://markaby.github.io) templates
- email auth with a migration, model, a mailer and a background job ([sucker_punch](https://github.com/brandonhilkert/sucker_punch)) for that mailer

I feel like this is pretty close to what rails gives you but without all of the [omakase](https://dhh.dk/2012/rails-is-omakase.html) and higher memory usage.
