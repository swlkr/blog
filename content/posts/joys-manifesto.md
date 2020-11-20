---
title: Joy Web Framework Manifesto
date: 2020-10-22
---

# The Joy Web Framework Manifesto

I'm not really a manifesto person, but here it is anyway. Wasn't sure what else to call it. Principles maybe?

- RFD (routes, functions, database)
- LoB (locality of behavior)
- MOIST (move other interdependent stacks together)

## RFD as opposed to MVC

Routes, Functions and Database

Usually you map controllers and models to database tables, but then it starts falling apart when you want something that combines multiple tables together or is a weird mix of having to modify n tables in a single request. So you wind up with concerns and other resources or service models that are backed by multiple tables or materialized views or something.

Joy instead treats the database as one thing. Every database actions lives under `db/` You don’t need to fuss about with trying to come up with abstract “resources” to represent controllers/models/views. You can instead define one route that maps to one action that can take as many varied paths to the database as necessary.

## Locality of Behavior

Dont sweat over separation of concerns. Instead focus on how to make code more readable by putting related code close together in one file. An example of LoB is a typical Joy controller function:

```clojure
(defn new [request &opt errors]
  (let [post (body request)]

    [:form {:method :post :action "/posts"}
      [:input {:type "text" :name "name" :value (post :name)}]
      [:div (errors :name)]

      [:textarea {:name "body"} (post :body)]
      [:div (errors :body)]

      [:input {:type "submit" :value "Save"}]]))
```

The html (view) and the database access (controller/model) code live along side each other so when it comes time to understand “what happens when I visit this url?” its all right there. You don’t have to tab between multiple files in your editor.

## Move Other Interdependent Stacks Together

That last and final weird acronym in Joy's arsenal is MOIST. Don't separate the backend from the frontend. Don’t even separate css js and html! Browsers are *great* at rendering html directly from a response. You don't need to treat the backend as an api that returns json in graphql queries, you can remove whole layers of abstraction if you put html on the wire and have fragments of your page update. HTMX does this with a very simple API:

```clojure
[:button {:hx-get "/click" :hx-target "outerHTML"} "Click me"]
```

This tells the browser that when you click this button, replace that button with the html from the response. That's exactly what MOIST and LoB is all about. Everything that is dependent on something else should be together in the same file and if it works, the same line of code. By moving things together you are essentially creating a domain specific language for working with the web. At the same time, you are making it easier on your future self, when you come back to a line of code you don't need to go anywhere else to see what it does! Don't pull things apart just because they look different syntactically. HTML, CSS, Javascript, Janet and SQL can and should all live together in the same function. Only extract + reference things when and where it makes sense, but before you start making references and splitting things up try to make things shorter but still understandable first.
