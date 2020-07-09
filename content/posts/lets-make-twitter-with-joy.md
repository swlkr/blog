---
title: Lets Make Twitter With Joy
date: 2020-07-09
---

# Let's make twitter with joy

Welcome to a series I call __with joy__ where I clone popular websites/webapps with my web framework, [joy](https://github.com/joy-framework/joy).

The world needs more twitter fights, misinformation and emotionally charged tweet storms, so let’s make another twitter using my web framework joy.

You’re gonna love my tweets.

## First things first

[Install janet](https://janet-lang.org/docs/index.html) if you're on a mac just do this:

```sh
brew install janet
```

Now install joy

```sh
jpm install joy
```

If joy isn't on your path, make sure it gets there:

```sh
echo 'PATH=/usr/local/Cellar/janet/1.10.1/bin:$PATH' >> ~/.zprofile
source ~/.zprofile
```

That's it, we're up and running!
Generate a new joy project:

```sh
joy new patter
```

Alright so this generates a folder structure and files and everything are everywhere, but you really only need a few files to make joy work as a web framework:

1. `main.janet`
2. `public/`
3. `.env` <-- don't check this in to source control
4. `project.janet`

I like to have everything in one flat directory, and all source code in one file, multiple source files takes me out of the zone. So I’m going to delete a bunch of files and I’m left with the simplest database backed web application ever:

```sh
# .env (again, ignore this in source control) echo .env >> .gitignore

ENCRYPTION_KEY=2d7a3200d40b6905e813474e1514c58dc9eb1cb0a8ea78ab5baba2b70fcfcc2c
JOY_ENV=development
PORT=9001
```

The `ENCRYPTION_KEY` is used for session encryption in cookies which I'm fairly sure works for the most part. Pretty sure, yeah.

The `public/` folder is for files that will be served to the browser as is.

```clojure
; # project.janet

(declare-project
  :name "patter"
  :description "A twitter clone made with joy"
  :dependencies ["https://github.com/joy-framework/joy"]
  :author ""
  :license ""
  :url ""
  :repo "https://github.com/swlkr/patter")

(declare-executable
  :name "patter"
  :entry "main.janet")

(phony "server" []
  (os/shell "janet main.janet"))

(phony "watch" []
  (os/shell "ls | entr -r -d janet main.janet"))
```

And finally the actual source code for the web server all in one file, just like the good old PHP days, except with more trailing parens:

```clojure
; # main.janet

(import dotenv)
(dotenv/load)
(use joy)


(def port (os/getenv "PORT"))


(route :get "/" :home)
(defn home [request]
  (text/plain "patter is up and running"))


(def app (app))


(defn main [& args]
  (server app port))
```

Let’s make sure it still works:

```sh
joy server
# => [2020-07-09 22:18:11] method=GET uri=/ content-type=text/plain status=200 duration=0.8ms
```

And then in another tab

```sh
curl -v localhost:9001

* Connected to localhost (127.0.0.1) port 9001 #0
> GET / HTTP/1.1
> Host: localhost:9001
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 200 OK
< Set-Cookie: id=d0fde19a1945603a38a2c2b446e7352f59a84812ac7711a617d80022b9a04b37c89a5d6e1fef97df14c9eaa09f3e2c8017b8dc7e50bb019c97b8e1c36e611ebba4b008dd1e258dba21c4c5dcf466285d91ffbad5; SameSite=Lax; HttpOnly; Path=/
< X-Frame-Options: SAMEORIGIN
< X-Permitted-Cross-Domain-Policies: none
< X-Download-Options: noopen
< Referrer-Policy: strict-origin-when-cross-origin
< Content-Type: text/plain
< X-Content-Type-Options: nosniff
< X-XSS-Protection: 1; mode=block
< Content-Length: 24
<
* Connection #0 to host localhost left intact
patter is up and running* Closing connection 0
```

So that should do two things:

1. return the response with "patter is up and running"
2. also show a log line for the request/response in the tab where `joy server` is running.

Now that everything is working, let's talk databases in part 2.
