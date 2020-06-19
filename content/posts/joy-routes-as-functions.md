---
title: Joy Routes as Functions
date: 2020-05-14
---

Joy Routes as Functions

I'm working on a community site for janet documentation and examples, kind of like clojuredocs, but with more janet.

I'm also trying to livestream or at least record me working on it and one thing I really wanted to focus on was rapid development.
What is the least amount of keystrokes I can make to get something up and running with joy. I've made a few changes to joy recently that really helps any dev move very quickly with routing.

In janet, almost every character can be used in a function name, including a "/" which took me a while to really consider what that meant since I was used to "/" having significance in clojure. It was a crazy idea but it seems to be working. One side effect is that there isn't the same ordering as before, so if you put a route with a param in it like `/:id` it will match any route with one segment, like `/1` or `/accounts` which isn't that great.

It's a decent trade off though if you prefix things by table name anyway. Here's an actual joy program:

    (use joy)

    (defn layout [{:body body}]
      (text/html
        (doctype :html)
        [:html
         [:head
          [:title "JanetDocs"]
          (link {:href ["/_pylon.css" "/_water.css" "app.css"]})
          (script {:src ["/_app.js" "/alpine.js"] :defer ""})]
         [:body
          body]]))


    (defn / [request]
      [:h1
       [:span "JanetDocs is a community website for the "]
       [:a {:href (url-for :/)}
        "janet programming language"]])


    (defn /404 [request]
      (layout
        {:body [:h1 "/404"]}))


    (def app (app {:layout layout
                   :404 /404}))


    (defn main [& args]
      (server app 9001))

Crazy right? 23 lines of code (minus newlines) and you get 404s, a home page and layouts. Function routing will probably be my go to from now on. I also like having everything in a single file which really helps me move quickly. I don't have any particular problem with files that ~2,000 lines long

I think the near future of programming will be to abstract the files/folders metaphors away and just have code blocks (functions) floating in a text editor ordered by reference/call stack, similar to what the original version of light table was or I guess what darklang is going for.

Anyway, let me know what you think on [twitter](https://twitter.com/swlkr) or drop `@swlkr` in the [janet gitter chat](https://gitter.im/janet-language/community).

Oh yeah and watch out for my videos on youtube, hopefully my boring office setup doesn't take away from the sweet, sweet hacking that will take place.
