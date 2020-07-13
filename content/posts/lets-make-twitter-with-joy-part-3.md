---
title: "Lets Make Twitter With Joy: Part 3"
date: 2020-07-12
---

# Let's make twitter with joy: part 3

Welcome to a series I call __with joy__ where I clone popular websites/webapps with my web framework, [joy](https://github.com/joy-framework/joy).

If you're just tuning in [Part 2](/posts/lets-make-twitter-with-joy-part-2/) was a little bit of a let down. It left you with a schema for patter, (the twitter clone) but no migrations. Let's fix that

## Migration city

Usually this makes more sense when I'm kind of going table by table but since I already have the whole schema ready to roll, I'll just show you how to generate all of the migrations and be done with it:

```sh
joy create table account 'name text unique not null' 'display_name text' 'photo_url text'

joy create table post 'account_id integer not null references account(id)' \
                      'body text not null'

joy create table tag 'name text unique not null'

joy create table post_tag 'post_id integer not null references post(id)' \
                          'tag_id integer not null references tag(id)'

joy create table follow 'follower_id integer not null references account(id)' \
                        'followed_id integer not null references account(id)' \
                        'unique(follower_id, followed_id)'

joy create table like 'account_id integer not null references account(id)' \
                      'post_id integer not null references post(id)' \
                      'unique(account_id, post_id)'
```

For follow and like, joy isn't smart enough yet to put the compound unique constraints at the end, so go in to each file and change it around.

After you run all of those, run:

```sh
joy migrate
```

And you should have a sqlite database file in the db folder AND a new `schema.sql` file as well that has those tables AND you should also be ready to rock and roll because we're about to put this twitter clone's ui together!

Starting with the timeline or the feed, or whatever.

## UI City

Before we make this timeline, we need to do two things:

1. Design it figma
2. Create some fake users and fake posts in the repl

I've taken the liberty of designing it in figma for you, so you can just sit back and relax:

![a poorly made clone of twitter's timeline with two posts saying "this is the first pat in patter" and "this is the second pat in patter"](/lets-make-twitter-part-3.png)

Look familiar? You bet it does. This is a twitter clone after all.

Quickest design ever, now let's make some fake users in the repl:

```sh
jpm install https://github.com/joy-framework/http
```

this installs a [libcurl](https://curl.haxx.se/libcurl/) wrapper that lets you make http requests from your janet program. You know what, let's also install [rlwrap](https://github.com/hanslub42/rlwrap):

```sh
brew install rlwrap
```

This is going to make the repl experience much nicer with arrow keys support and -s.

*Now* open up the repl

```sh
rlwrap janet -s
# =>
# Janet 1.10.1-meson  Copyright (C) 2017-2020 Calvin Rose
# janet:1:>
```

The -s just gives the ability to paste multiple lines.
Now type this into the janet repl:

```clojure
(import http)
(import json)
(use joy)

(def http-response (http/get "https://randomuser.me/api/?results=10" :headers {"Content-Type" "application/json" "Accept" "application/json"}))
(def body (http-response :body))
(def response (json/decode body true true))
(def results (response :results))

(db/connect (env :database-url))

(loop [result :in results]
  (db/insert :account {:name (get-in result [:login :username])
                       :photo-url (get-in result [:picture :medium])}))
```

Go ahead and check that the code above did what it was supposed to do:

```clojure
(printf "%M" (db/fetch-all [:account]))
```

There should be a few accounts in there with some photos and names. Now let's generate some fake posts:

```clojure
(def accounts (db/fetch-all [:account]))

(def fake-posts
  (as-> (http/get "http://localhost:1313/fake-posts.txt") ?
        (get ? :body)
        (string/split "\n" ?)
        (filter |(not (empty? $)) ?)))

(def rng (math/rng))

(defn random-fake-post []
  (let [post-idx (math/rng-int rng (length fake-posts))]
    (get fake-posts post-idx)))

(defn random-account []
  (let [account-idx (math/rng-int rng (length accounts))]
    (get accounts account-idx)))

(loop [account :in accounts]
  (let [num-posts (math/rng-int rng 5)]
    (loop [idx :range [0 num-posts]]
      (let [account (random-account)]
        (db/insert :post {:account-id (account :id)
                          :body (random-fake-post)})))))
```

Give that a check to see if it's working:

```clojure
(db/fetch-all [:account 1 :post])
```

You should see an array of post dictionaries there or if it isn't there, check another account by changing that integer.

We made quite a bit of progress on the data side, now let's slap some of this stuff into the ui and hope it turns out like that design I showed you earlier.

Putting things into hiccup is straightforward, there is no other syntax to learn since it's just janet:

```clojure
(let [name "sean"]
  [:div name])
```

Returns

```html
<div>
  sean
</div>
```

Here's one way to put a few items in a list:

```clojure
(let [names ["forest" "jade" "autumn"]]
  [:ul
    (foreach [name names]
      [:li name])])
```

Outputs:

```html
<ul>
  <li>
    forest
  </li>
  <li>
    jade
  </li>
  <li>
    autumn
  </li>
</ul>  
```

You could also use `map` instead of joy's `foreach`, it's up to you.

Alright so let me walk you through how I approach these mundane figma to html conversions:

1. use SwiftUI primitives a-la `vstack` and `hstack`
2. outline the original image with a plan (usually in my head)

For the purposes of this post, I'll outline each different area of the image to show you my thought process:

![colorful rectangles outlining each vstack/hstack of the twitter clone's ui](/lets-make-twitter-part-3-vstack-hstack.png)

I should work on a figma to SwiftUI conversion plugin, but for now, we'll just type it out manually.

```clojure
[:vstack
 (foreach [post posts]
   (let [{:account account} post]
     [:hstack
      [:img {:src (account :photo-url)}]

      [:vstack
       [:hstack {:spacing "s"}
        (account :display-name)
        (account :name)
        [:spacer]
        [:time {:class "tr"} (post :created-at))]

       (post :body)

       [:hstack {:stretch ""}
        (icon :reply)
        (icon :repeat)
        (icon :heart)]]]]))]
```

So hopefully you can sort of see the layout in the html. Also notice that with ridge, I didn't write a single line of css, no css grid, no flexbox styles, just vstack/hstack for layout. It combines the best of table layouts from 90s with flexbox responsive styles of the mid 2010s.

I want to talk about how we get multiple tables into an array, because it's pretty important and this is something that happens across apps and sets traditional three-tier apps apart from spreadsheets and most of the no-code tools today.

## ORMs or lack thereof

Typically, in most web frameworks, you are constrained to classes, objects and the mapping back and forth from the database to those objects. I like constraints, but I don't like the wrong ones, and I think ORMs make mountains out of mole hills. Here's how I overcome some of the constraints of only having functions and dictionaries in joy:

### Multiple tables in one array of dictionaries

In this case, we have the `post` table and the `account` table and we would like to loop through all of the posts in reverse chronological order (newest first) and also show who posted it.

Laying that out is kind of a mouthful, but here's how it works in practice:

```clojure
(def posts (db/fetch-all [:post]
                         :order "created_at desc"
                         :limit 10))

(def accounts (db/fetch-all [:account]))

(defn set-account [accounts row]
  (if (row :account-id)
    (set row :account (find |(= ($ :id) (row :account-id))
                            accounts))
    row))

(map |(set-account accounts $) posts)
```

It's kind of repetitive and not that great, let's fix it so it works for any table one level deep:

```clojure
(def posts (db/fetch-all [:post]
                         :order "created_at desc"
                         :limit 10))

(def accounts (db/fetch-all [:account]))

(def lift [table fk-rows row]
  (if-let [fk-col (keyword (string table "-id"))
           fk-id (get row fk-col)
           fk-row (find |(= fk-id ($ :id)) fk-rows)]
    (set row table fk-row)
    row))

(map |(lift :account accounts row) posts)
```

It's more complex, but it does the trick for any two tables (assuming joy's convention of id for column and foreign key names). Both of these things kind of hide what we're really trying to do and they only work for a very small set of data. At this point I would normally either defer to sql/joins or just take advantage of the fact that [n+1's aren't really a problem for sqlite](https://www.sqlite.org/np1queryprob.html). We'll do the sql + joins thing first since it is the most efficient solution:

```sql
/* db/timeline.sql */

select
  post.id,
  post.body,
  post.updated_at,
  post.created_at,
  account.name,
  account.display_name
from
  post
join
  account on account.id = post.account_id
order by
  post.created_at desc
limit
  10
```

and then to use that in joy:

```clojure
(def posts (db/query (slurp "db/timeline.sql")))
```

And there is no more preprocessing since we did everything in sql. Super efficient, works for much larger amounts of data, and accessing the foreign key table is as simple as `(post :account-name)` or `(post :account-display-name)`.

Here's the other solution where you're trusting sqlite to do 10 queries on a page:

```clojure
(def posts (db/fetch-all [:post]
                         :order "created_at desc"
                         :limit 10))

(foreach [post posts]
  (let [account (db/find :account (post :account-id))]
    ...))
```

Also very simple, even simpler because we didn't have to write any sql at all, BUT I think the response time of the local server gives this away a little bit. Let me show you what the main file looks like now and some of the response times I'm seeing with both implementations:

```clojure
(use joy)


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

     [:body {:class "bg-background-alt"}
      body]]))


(route :get "/" :home)
(defn home [request]
  (def rows (db/query (slurp "db/sql/timeline.sql")))

  [:vstack {:spacing "m" :class "mw-xxl mx-auto pb-m"}
   (foreach [row rows]
     [:hstack {:spacing "xs" :align-y "top" :class "bg-background pa-xs br-5"}
      [:img {:src (row :account-photo-url) :class "br-100"}]

      [:vstack {:spacing "xs"}
       [:hstack {:spacing "s"}
        (row :account-display-name)
        [:div {:class "muted"}
         (string "@" (row :account-name))]
        [:spacer]
        [:time {:data-seconds (row :created-at) :class "muted tr"}
         (row :created-at)]]

       [:div {:class "h-l"}
         (row :body)]

       [:hstack {:stretch ""}
        (icon :reply)
        [:spacer]
        (icon :arrow-repeat)
        [:spacer]
        (icon :heart)]]])])


(def app (app {:layout layout}))


(defn main [& args]
  (db/connect (env :database-url))
  (server app (env :port)))
```

N+1 response times are around 30ms, join response times are around 26ms. So, not the best either way, but not too much difference. I usually stick with the n+1 because I put sqlite straight into "production". Sounds like heresy, I know.

Alright, so part 4 will be adding new posts and show you how htmx and joy get along.
