---
title: Reddit Tiktok
date: 2021-02-10
---

# Reddit Tiktok

I mashed reddit and tiktok's ui together, complete with css scroll snap, and lazy loading on each swipe.

It's not really a serious project, more like an example of what a mobile first, text first
platform could look like. Without further ado:


{{< figure src="/reddit-tiktok.gif" title="" width="250" >}}

The source is [here](https://github.com/swlkr/reddit-tiktok)

I want to walk through the stack this project uses and how it could make deploying easy.

## The stack

This is in the readme as well, so didn't want to go too much in depth here, but:

- sqlheavy (sqlite, database)
- tw (css, tailwind)
- osprey (html, http server)
- vanilla js

## The code

The majority of the code is in one file, `main.janet`

It's laid out like so:

1. js cache busting macro
2. database stuff (schema + "model")
3. reddit importing
4. html rendering
5. tailwind dead code elimination
6. http server

There's about 130 lines total, but I think the gist of what this stack brings can be summed up here:

### The stack section

```clojure
(import osprey :prefix "")
(import tw :prefix "")
(import sqlheavy/sqlheavy :prefix "")
```

This is the "stack" in code. I don't use prefixes because I wrote everything and know what functions exist
in which libraries, but if you're just getting started, feel free to use the prefixed versions.

### The database section

```clojure
; # database schema
(db/query `
create table if not exists items (
  id integer primary key,
  title text,
  url text,
  author text,
  author_url text,
  posted_at integer,
  created_at integer default(strftime('%s', 'now')))`)

; # database crud methods
(defmodel Item)
```

This creates one table and one model. Model here is just a fancy way of saying object with five methods on it. Check the
sqlheavy docs for more info there.

### The UI section

I'm going to skip the reddit importing stuff and just show you how to output html with that data

```clojure
(enable :static-files)


; #(def -layout-)
(layout
  (doctype :html5)
  ; # the tw macro is used wherever you want to
  ; # use tailwind classes. it works together
  ; # with tw/href and tailwind-min-css
  ; # to generate a css file in public/
  ; # with only the stuff you use
  [:html {:class (tw "relative w-full h-full p-0 m-0")}
    [:head
     [:meta {:charset "utf-8"}]
     [:title "reddit-tiktok"]

     ; # this serves the tw.<hex value>.css file
     ; # generated from tailwind-min-css
     [:link {:rel "stylesheet" :href tw/href}]

     ; # this bundle macro reads the public/all.js
     ; # file and hashes the contents and outputs
     ; # a bundle.<hex value>.js file
     ; # poor man's cache busting
     [:script {:src (bundle "/all.js") :defer ""}]]
    [:body {:class (tw "relative w-full h-full p-0 m-0")}
     response]])


(defn section [item]
  [:section {:class (tw "flex flex-col justify-center items-center h-screen w-full snap-start px-4")}
    [:h1 {:class (tw "text-2xl dark:text-white")}
     [:a {:href (item :url)
          :class (tw "underline")}
      (string/replace-all "&amp;" "&" (item :title))]]
    [:h2 {:class (tw "text-lg self-start dark:text-white")}
     [:a {:href (item :author_url)
          :class (tw "underline")}
      (item :author)]]])


(GET "/"
  (let [items (db/query "select * from items order by random() limit 2")]
    [:main {:class (tw "overflow-y-scroll snap snap-y snap-mandatory max-h-full max-w-lg mx-auto dark:bg-gray-800")}
      (map section items)]))


(GET "/next"
  (use-layout false)
  (content-type "text/html")

  (let [item (first (db/query "select * from items order by random() limit 1"))]
    (html/encode
      (section item))))

; # generated, minified tailwind.css file, ~5MB
; # this gets turned into a few kb with this line
; # outputs a tw.<hex value>.css file in public/
(tailwind-min-css "tailwind.min.css")
```

### The http server section

This just starts the osprey http server and serves those two routes, / and /next

```clojure
(server 9001)
```
