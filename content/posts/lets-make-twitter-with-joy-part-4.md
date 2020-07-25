---
title: "Lets Make Twitter With Joy: Part 4"
date: 2020-07-23
---

# Let's make twitter with joy: part 4

Welcome to a series I call __with joy__ where I clone popular websites/webapps with my web framework, [joy](https://github.com/joy-framework/joy).

If you're just tuning in [Part 3](/posts/lets-make-twitter-with-joy-part-3/)
was kind of complicated, I went from database schema/migrations to UI to ORM and back again. In this part, I want to slow down and talk about the bigger picture.

## The bigger picture

What am I really doing here? What is the meaning of life, really?

I'm kidding, not that big picture, I'm going to zoom in a little bit to just this project.

Anytime you make a website, there are a lot of pieces that get put together. After I really started to get web development, I pretty quickly came to the conclusion that it's a blue collar job with a little better pay (if you can manage it). You basically need to plumb different existing pieces of software together to make something whole. Here are main parts of what make a dynamic, database-backed website today:

- Database (obvs)
- Some language that outputs html (or json if you go the react/vue/svelte route)
- CSS (for flair)
- JS (for that extra interactivity without full page loads, so for speed)
- UI/UX Design (for flair again)

Each of these pieces can be broken down further:

- database - sqlite/postgres/mysql/sql server
- lang - janet + joy (for this project)
- css - atomic css + pylon css + classless css (ridge.css)
- js - htmx

I can go further, but I don't want to get bogged down in tooling anymore than I have to. The goal is to make a twitter clone, I'll use the fastest way I know how to get there, if that's GPT-3 or what have you, I'll do it.

Alright, so now that this stuff is all laid out on the table, this is how it connects and pretty much how it always connects across everything the user does

```clojure
(route :get "/posts" :posts/index)
(route :get "/posts/:id" :posts/show)
(route :get "/posts/new" :posts/new)
(route :post "/posts" :posts/create)
(route :get "/posts/:id/edit" :posts/edit)
(route :patch "/posts/:id" :posts/patch)
(route :delete "/posts/:id" :posts/delete)
```

Similar to other web frameworks, this is pretty much it. These routes are accompanied by functions named the same name as the last thing after the colon. So `:posts/index` would be `(defn posts/index [req])` each of the functions also always takes a request dictionary.

```clojure
(route :get "/posts" :posts/index)
(defn posts/index [request]
  ; # here is where the database is called
  (def posts (db/from :posts
                      :join :accounts
                      :order "created_at desc"
                      :limit 15))

  ; # this is where the html is output
  [:vstack
    (foreach [post posts]
      [:vstack
        [:div (post :body)]
        [:div (get-in post [:account :name])]
        [:time (post :created-at)]])])
```

Some of the html looks funny because of hiccup, which I went over in one of the previous parts, I forget where, but basically the goal is to replicate SwiftUI as much as possible and abandon all web best practices.

There's an easier to understand version of this which is a json api:

```clojure
(route :get "/posts" :posts/index)

(defn posts/index [req]
  (let [posts (db/from :posts
                       :join :accounts
                       :order "posts.created_at desc"
                       :limit 10)]
    (application/json posts)))
```

There's an even easier version of this where you don't need to name the intermediate `posts` variable or the `req` variable:

```clojure
(route :get "/posts" :posts/index)
(defn posts/index [_]
  (->> (db/from :posts
                :join :accounts
                :order "posts.created_at desc"
                :limit 10)
       (application/json)))
```

Getting data into the database is a two part situation that always looks like this, it might be a little different if you want to return a modal or something but for the most part it's this:

```clojure
(route :get "/posts/new" :posts/new)
(defn posts/new [req]
  [:vstack
    (form-with req (action-for :posts/create)
      [:vstack
        [:textarea {:name "body" :rows 5}]
        (when-let [error (get-in req [:errors :body])]
          [:div {:class "error"} error])]
      [:button {:type "submit"} "Create"])])

; # the other side of this is the action-for part, :posts/create

(route :posts "/posts" :posts/create)
(defn posts/create [req]
  (let [[errors post] (->> (params req)
                           (merge (req :account)
                           (db/insert)
                           (rescue)))])
    (if errors
      (posts/new (put req :errors errors))
      (redirect-to :posts/index)))
```

It's not the prettiest but it works pretty well, and you can copy that validation code to a function to get re-use across both `create` and `patch` functions.

Here's a more complete example of a json api only:

```clojure
(use joy)


; # middleware runs before every handler
(defn check-api-key [handler]
  (fn [request]
    (if-let [account (db/find :accounts (headers req :x-api-key))]
      (handler (merge request {:account account}))
      (application/json {:errors {:x-api-key "you need to send a valid x-api-key header"}}))))


; # posts "controller" code
(route :get "/posts" :posts/index)
(route :post "/posts" :posts/create)
(route :patch "/posts/:id" :posts/patch)
(route :delete "/posts/:id" :posts/delete)

(defn params [req]
  (def body (req :body))
  (def post @{:body (body :body)
              :db/table :posts})

  (when (empty? (post :body))
    (raise {:body "body is required"})

  post))

; # this above params pattern is so common
; # joy has it built in

; # params takes the name of the table
; # validates takes a tuple of column names and a validator, :required, :email, :min-length, :max-length, :uri, :matches
; # and compares the value from the form to that validator
; # permit takes away all keys from the form except for the ones specified in the tuple, this is optional
(def params
  (params :posts
    (validates [:body] :required true)
    (permit [:body])))


(defn post [{:account a :params p}]
  (db/fetch [:accounts a :posts p]))


(defn posts/index [_]
  (-> (db/from :posts :join :accounts
                      :order "created_at desc"
                      :limit 15)
      (application/json)))


(defn posts/create [req]
  (let [[errors post] (-> (params req)
                          (put :account (req :account))
                          (db/insert)
                          (rescue))]
    (if errors
      (application/json {:errors errors} :status 422)
      (application/json post))))


(defn posts/patch [req]
  (when-let [post (post req)
             [errors post] (->> (params req)
                                (merge post)
                                (db/update)
                                (rescue))]
    (if errors
      (application/json {:errors errors} :status 422)
      (application/json post))))


(defn posts/delete [req]
  (when-let [post (post req)
             [errors post] (rescue (db/delete post))]
    (application/json post)
    (application/json {:errors "could not delete post"} :status 500)))


(def app (-> (app)
             (check-api-key)))


(defn main [& args]
  (server app 9001))
```

Usually things are more interesting than this, but not by much. The thing that I think sets joy apart from other web frameworks is you have a little give in the structure of things, I personally like all of my db code and my controller code and my view code all in one file, similar to PHP or some spaghetti code of yesteryear.

Alright so now that the structure is mostly out of the way, in part 5 I really want to move fast and get most of the plumbing done, if not the whole thing. Stay tuned.
