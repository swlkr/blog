---
title: A Rails Dockerfile
date: 2022-01-31
---

# A Rails Dockerfile

I recently switched back to rails to actually ship some websites. I don't use rbenv or anything like that on my development machine, I use Docker everywhere. Here's a naive Dockerfile I've been using in development:

```sh
FROM docker.io/library/ruby:3.0.3-slim

RUN apt-get update -qq && apt-get install -y --no-install-recommends curl build-essential git-core libjemalloc2 libsqlite3-dev && rm -rf /var/lib/apt/lists/*

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

ENTRYPOINT ["bin/docker/entrypoint"]

CMD ["bundle", "exec", "puma"]
```

I also have one for production, but don't think it's anything to write home about, but I'll put it here anyway:

```sh
FROM docker.io/library/ruby:3.0.3-slim AS base

RUN apt-get update -qq && apt-get install -y --no-install-recommends curl build-essential git-core libjemalloc2 libsqlite3-dev && rm -rf /var/lib/apt/lists/*

ENV LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libjemalloc.so.2

FROM base AS dependencies

COPY Gemfile Gemfile.lock ./

RUN bundle config set without "development test" && bundle install --jobs=3 --retry=3

FROM base

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

COPY --from=dependencies /usr/local/bundle/ /usr/local/bundle/
COPY --chown=$USER . /var/app

CMD ["bundle", "exec", "puma"]
```

It's pretty heavy, comes out to around ~500MB, could use some work on the size, might just abandon `jemalloc` since I'm not even sure if it's working and alpine linux is way, way smaller.
