---
title: A very simple roda dockerfile for development
date: 2021-06-16
---

# A very simple roda dockerfile for development

I couldn't find a great guide for using docker / docker compose with roda in development so here's my attempt.

Here's the Dockerfile:

```
FROM ruby:3.0.1-slim-buster

RUN apt-get update -qq && apt-get install -y build-essential libsqlite3-dev

RUN apt-get install -y --no-install-recommends libjemalloc2
RUN rm -rf /var/lib/apt/lists/*

ENV LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libjemalloc.so.2

ARG USER=app
ARG GROUP=app
ARG UID=1101
ARG GID=1101

RUN groupadd --gid $GID $GROUP
RUN useradd --uid $UID --gid $GID --groups $GROUP -ms /bin/bash $USER

RUN mkdir -p /var/app
RUN chown -R $USER:$GROUP /var/app

USER $USER
WORKDIR /var/app

COPY --chown=$USER Gemfile* /var/app/
RUN bundle install

COPY --chown=$USER . /var/app

CMD ["rerun", "--pattern", "rb,js,css,mab,ru", "--force-polling", "--", "rackup", "-o", "0.0.0.0"]
```

This uses debian slim, jemalloc, and it assumes you already have a Gemfile and it assumes you want to use rerun instead
of unreloader or zeitwerk or something.

I understand alpine is smaller, and I understand multistage builds, but this is for development, not production.

## compose

Here's the docker-compose file:

```
version: '3'
services:
  web:
    tty: true
    env_file: .env
    volumes:
      - .:/var/app
    build: .
    ports:
      - "9292:9292"
```

That's it! It just copies everything to the local directory, reads a `.env` file for env vars and exposes the default rack port of 9292.

This is what ships with [roda starter](https://github.com/swlkr/roda-starter) by the way.
