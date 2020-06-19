---
title: Making Low Memory Websites
date: 2020-04-22
---

I've been working on my second home grown janet web framework so I can run multiple websites on a single raspberry pi in my closet.

My first web framework in this vein (which I'm still working on) is [joy](https://github.com/joy-framework/joy).

So, you know go smash that star button on github or something.

This next web framework does a lot less. It doesn't have database stuff built in, you can't control the middleware very well, and it requires a lot less typing than joy. You can also write everything in one file if you want. It’s a very dynamic framework that leans heavily on macros and global variables and does some things that are probably frowned upon, but oh well. Let’s take a look:


    (use trout)

    (route/get "/"
      "hello world")

    (server/start 8000)

That’s the whole thing. You don’t have to write any other code to have a working web server. That brings it down to 4 lines of code for a very basic plaintext serving website. Pretty cool. Of course that’s not super useful, it would be better if the thing could actually do something useful like submit a form with a csrf token:

    (use trout)

    (enable :sessions)
    (enable :csrf-tokens)

    (def todos/new
     (route/get "/todos/new"
       (let [errors (request :errors)]
         (form {:method "POST" :action "/todos"}
           [:input {:type "text" :name "name"}]
           [:div (errors :name)]))))

    (route/post "/todos"
      (if (body :name)
         (redirect "/")
         (todos/new (put-in request [:errors :name] "Name can’t be blank"))))

There's a bit more going on here the one thing that stands out is that the html has a `(form)` function that creates a hidden input with a csrf token that gets encrypted and stored in a cookie, this also assumes an environment variable named `ENCRYPTION_KEY` that relies on [cipher](https://github.com/joy-framework/cipher) for encryption/decryption. That takes care of that. Oh the cookie is also set with SameSite=Lax; HttpOnly; so that's good.

One more thing that allows you to do some interesting things like auth are before filters:


    (route/before "/"
      (when-let [id (get-in session [:account :id])
                 account (db/find :account id)]
        (put request :account account)))


This bit of code runs before all routes that begin with `/` (so all routes) and sets the current account in the request var if there is one in the session cookie.

You can check the account in a route like this:


    (route/get "/requires-account"
      (when (request :account)
        [:div "You're logged in!"]))


This will either return `nil` from `when` which will go on to return a 404 response or return a div with "You're logged in!". One line to check if someone is authenticated or not, pretty nifty.

There are a few other things I'm looking at stealing from [sinatra](https://github.com/sinatra/sinatra) and a few more things that will be new, but the idea is to keep the scope super small and see how fast (if it is any faster in the end) I can make new websites and how little memory they use in "production".

There are a few more things related to middleware like layouts and built in [turbolinks](https://github.com/turbolinks/turbolinks) support that I'll get to in a more in depth post. Oh and also `trout` is a working name and it's not open source yet, I'm still trying to figure out ways to make website development even faster.
