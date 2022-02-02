---
title: Rails Docker Development
date: 2022-02-02
---

# How to develop ruby on rails on docker

I hate the idea that if I ever have to switch to another computer or something, like for example from my macbook pro to a framework laptop running mint or popOS or something, I'll have to set my whole dev environment up again. That stinks, I don't want that. So I use docker in development AND docker on the server via dokku. I'm always looking for better container runtimes like podman or colima but until I get to a completely linux dev environment, I'll stick with docker desktop.

Alright, so starting a new rails 7 project with docker is not quite as simple as rbenv ... rails new but it's close. Here's what I do:

```sh
mkdir <your_project>
cd <your_project>
mkdir -p bin/docker
touch Dockerfile Gemfile Gemfile.lock .env
```

Put this in your `Dockerfile` or call it `Dockerfile.dev`

```sh
FROM docker.io/library/ruby:3.0.3-slim

RUN apt-get update -qq && apt-get install -y --no-install-recommends curl build-essential git-core libjemalloc2 libsqlite3-dev && rm -rf /var/lib/apt/lists/*

ENV LD_PRELOAD /usr/lib/x86_64-linux-gnu/libjemalloc.so.2

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

Put this in your `Gemfile`

```ruby
source "https://rubygems.org"
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby "3.0.3"

# Bundle edge Rails instead: gem "rails", github: "rails/rails", branch: "main"
gem "rails", "~> 7.0.1"
```

There are few docker scripts I use to make my life easier in `bin/docker` but I'll just talk about those in another post. For now, this is how I generate a new rails app via docker:

```sh
docker build -t <your_project> -f Dockerfile.dev .
docker run --rm -it -v $(pwd):/var/app --env-file .env <your_project> rails new --css=tailwind --skip-jbuilder --skip-system-test --skip-action-mailbox --skip-bundle --force .
```

After that runs you have to rebuild the dockerfile to run bundle install and install rails' dependencies:

```sh
docker build -t <your_project> -f Dockerfile.dev
```

Finally, you can boot your new rails app:

```sh
docker run --rm -it -v $(pwd):/var/app --env-file .env --publish 3000:3000 <your_project>
```

Unfortunately this doesn't run the tailwind stuff, that's in `Procfile.dev` so I download hivemind and then add a custom `bin/docker/up` script to handle that:

```sh
curl -L -O https://github.com/DarthSim/hivemind/releases/download/v1.1.0/hivemind-v1.1.0-macos-amd64.gz
tar xzf hivemind-v1.1.0-macos-adm64.gz
mv hivemind bin/hivemind
chmod +x bin/hivemind
touch bin/docker/up
```

Put this in `bin/docker/up`:

```sh
#!/bin/sh

docker run --rm -it -v $(pwd):/var/app --env-file=.env -p 3000:3000 --name "<your_project>" <your_project> bin/hivemind Procfile.dev
```

Now if you run `bin/docker/up` in your rails directory tailwind AND rails will start together!

All that work and your dev environment is now more or less portable, you'll still need hivemind but you can just copy it from project to project or put it in the `Dockerfile.dev`

