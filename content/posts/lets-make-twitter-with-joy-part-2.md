---
title: "Lets Make Twitter With Joy: Part 2"
date: 2020-07-11
---

# Let's make twitter with joy: part 2

Welcome to a series I call __with joy__ where I clone popular websites/webapps with my web framework, [joy](https://github.com/joy-framework/joy).

Alright, so [Part 1](/posts/lets-make-twitter-with-joy/) was kind of bust. It left you with one plaintext route and no database, even though I said the app was going to be backed by a database. Let's remedy that situation. This time we're going to do two things:

1. Start sending html like it's 1999
2. Apply our twitter clone database schema migrations

## Send HTML and make it pretty

Before you were calling `text/plain` which although it's good for debugging and [gopher](https://en.wikipedia.org/wiki/Gopher_(protocol)), not really ideal for browsers, can't style it and no images, and we want those because we're making the next great social network that's going to connect everybody and there won't be any conspiracy theories or hate-speech or thought-policing!

### Layouts

In joy you typically have one layout function which I affectionately call `layout`:

```clojure
(defn layout [{:body body :request request}]
  (text/html
    (doctype :html5)
    [:html {:lang "en"}
     [:head
      [:title "patter"]

      ; # meta
      ; # TODO: social
      [:meta {:charset "utf-8"}]
      [:meta {:name "viewport" :content "width=device-width, initial-scale=1"}]
      [:meta {:name "csrf-token" :content (authenticity-token request)}]

      ; # css
      [:link {:rel "stylesheet" :media "(prefers-color-scheme: light), (prefers-color-scheme: none)" :href "ridge-light.css"}]
      [:link {:rel "stylesheet" :media "(prefers-color-scheme: dark)" :href "ridge-dark.css"}]
      [:link {:rel "stylesheet" :href "/ridge.css"}]
      [:link {:rel "stylesheet" :href "/app.css"}]

      ; # js
      [:script {:src "/app.js" :defer ""}]]

     [:body body]]))
```

There are a few things here that are foreign, the use of [hiccup](https://github.com/weavejester/hiccup), [destructuring](https://janet-lang.org/docs/destructuring.html) function arguments, the `doctype` function, `text/html` function and the `authenticity-token` function. I'll go over each one right now:

- text/html

This one is pretty easy to suss out. It takes a variable number of arguments and outputs html, here are some examples:

```clojure
(text/html [:div]) ;# => <div></div>
(text/html [:div [:span "hello"]]) ;# => <div><span>hello</span></div>
(text/html [:div {:class "class"}]) ;# => <div class="class"></div>
(text/html [:div] [:div]) ;# => <div></div><div></div>
```

- doctype

This one is also straightforward it returns the doctype for a given version of html:

```clojure
(doctype :html5) ;# => "<!DOCTYPE HTML>"
(doctype :html4 :strict) ;# => "<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">"
```

So on and so forth, [check the source for the full list](https://github.com/joy-framework/joy/blob/master/src/joy/html.janet#L20)

- authenticity-token

This is anti cross site request forgery, the sessions are stored in encrypted cookies with SameSite=Lax by default, but it's always good to still use CSRF in case someone is using a browser that doesn't suppose SameSite yet.

- hiccup

It is strange at first, but after a while and after installing [parinfer](https://shaunlebron.github.io/parinfer/) on your editor, it's nice not to have to close those dangling open tags. Of course, if you're a pragmatic programmer, you know [you don't actually have to end those tags](https://gist.github.com/paulirish/1117438), but in hiccup you NEVER have to end tags:

```clojure
[:script {:src "/some-js-file.js"}] ;# => <script src="/some-js-file.js"></script>
```

Let's wire up the layout to `app` and get this thing going:

```clojure
; # layout function is here...

(route :get "/" :home)
(defn home [request]
  [:h1 "Welcome to patter"])


(def app (app {:layout layout}))

; # main function is here...
```

Make sure to test your changes, or if you're a skilled hacker, `brew install entr` to get the server to restart on any changes with `jpm run watch`. Either way, restart the server to pick up the changes and if you're using [ridge](https://ridgecss.com), like I am, you'll see this in your browser:

![a screenshot showing safari and a very blank white background with an h1 saying "welcome to patter"](/lets-make-twitter-part-2.png)

Now we're getting somewhere! Let's leave that for a second, maybe commit those changes and focus on the schema for now.

## Database schema

After thinking about this for a bit, twitter has quite a few more tables than I imagined at first, here's what I came up with for a bare bones clone:

```
account
- id integer primary key
- name text unique not null
- display_name text
- photo_url text
- updated_at integer
- created_at integer not null default(strftime('%s', 'now'))

post
- id integer primary key
- account_id integer not null references account(id)
- body text not null
- updated_at integer
- created_at integer not null default(strftime('%s', 'now'))

tag
- id integer primary key
- name text unique not null
- updated_at integer
- created_at integer not null default(strftime('%s', 'now'))

post_tag
- id integer primary key
- post_id integer not null references post(id)
- tag_id integer not null references tag(id)
- updated_at integer
- created_at integer not null default(strftime('%s', 'now'))

following
- id integer primary key
- follower_id integer not null references account(id)
- followed_id integer not null references account(id)
- updated_at integer
- created_at integer not null default(strftime('%s', 'now'))
- unique(follower_id, followed_id)

like
- id integer primary key
- account_id integer not null references account(id)
- post_id integer not null references post(id)
- updated_at integer
- created_at integer not null default(strftime('%s', 'now'))
- unique(account_id, post_id)
```

So barring any obvious problems like throttling, spam, or insane viral growth, this will do. There's a few things to note here:

- The table names are singular, not plural:

Is it the sock drawer or the drawer where you put socks? Plural database names aren't a hard and fast rule, and in most cases it increases complexity by quite a bit to map back to singular variable names.

- Timestamps (update/created_at columns) are integers

UTC epoch is the datetime format to end all datetime formats. Super easy to convert to other timezones, calculations like X days ago are super simple, javascript has great support for epoch. Stop storing datetime columns as human readable, give yourself a break.

This post is getting a little long, but I wanted to go over a sweet cli feature of joy's that makes this stuff really easy:

```sh
joy create table account 'name text unique not null' 'photo_url text'
```

This creates a database migration in the `db` folder that looks like this:

```sql
-- up
create table account (
  id integer primary key,
  name text unique not null,
  display_name text,
  photo_url text,
  created_at integer not null default(strftime('%s', 'now')),
  updated_at integer
)

-- down
drop table account
```

You get the timestamps, and the increasing integer primary key column by default! And it's sql and not some weird DSL! Incredible! In the next part, I'll go over running the migrations and we can get on to the ui stuff.
