---
title: RAD
date: 2021-01-24
---

# RAD: The alternative to MVC

No this isn’t me reminiscing about the good old days of [rapid application development](https://en.wikipedia.org/wiki/Rapid_application_development). This is me trying to come up with a cool, pointless acronym for an alternative way of making web applications.

## Routes Actions Data

You can think of this as "everything is in a rails controller method"

### Routes and Actions

Routes and actions are conceptually one thing.

- GET /list/todos
- GET /read/todo/:id
- GET /create/todo
- POST /create/todo
- GET /update/todo/:id
- POST /update/todo/:id
- POST /delete/todo/:id

The http method doesn't really matter anymore, it only matters if choosing between "I want html" or "I want to change some state" / "send some data with the request".

The url is what tells the whole story, your web application is now a list of actions the user can take, not a set of resources the user can take action on.

It's generally verbs vs nouns.

### Data(base)

This can be anything you'd like, but at the moment, I'm using a yesql inspired database library. You can think of it like "tailwind" for the database. You write plain old sql in a file to get syntax highlighting and use comments to name bits of sql that get turned into functions. It's crude but simple.

The only rule here is to have it be plain data, not objects.

```clojure
{:this "will work"}

[{:so "will"} {:this ""}]
```

Plain data, similar to json or ruby hash maps can be abstracted and operated on by functions or macros easily, unlike objects that are sort of the end of the line when it comes to composition.

Here's a concrete example:

```clojure
(import osprey :prefix "")
(import ./db)

; # database migrations
(db/create-table-todos)

; # routes
(def /list/todos "/list/todos")
(def /create/todo "/create/todo")

; # actions and database
(GET /list/todos
     (let [todos (db/todos)]
      [:ul
       (foreach [todo todos]
        [:li (todo :name)])]))


(GET /create/todo
     (form {:action /create/todo}

           [:label {:for "name"} "name"]
           [:input {:type "text" :name "name" :value (get body :name)}]

           [:a {:href /list/todos} "Cancel"]
           [:input {:type "submit" :value "Save"}]))


(POST /create/todo
      (def- body (table/slice body [:name]))

      (if (db/create-todo body))
          (redirect /list/todos)
          (render /create/todo)))
```

This is `db.janet`. The data layer is just this + 100 lines in suresql (and the [sqlite3 wrapper](https://github.com/janet-lang/sqlite3)). The implementation is super simple, even though that's not necessarily what I was aiming for.

```clojure
; # db.janet
(import suresql :prefix "")
(import sqlite3)

(def connection (sqlite3/open "db.sqlite3"))

(defqueries "todos.sql" {:connection connection})
```

Here's `todos.sql`:

[suresql](https://github.com/joy-framework/suresql) is just one approach, if you don't want to write sql there are other data access libraries you can use, or you can write your own!

```sql
-- name: create-table-todos
create table if not exists todos (
  id integer primary key,
  name text not null
)

-- name: todos
select *
from todos

-- name: create-todo
insert into todos (
  name
) values (
  :name
)
```

The moral of the story is, make web apps the way you want. Frameworks are nice but web apps are pretty easy to make, you might just want to write your own.
