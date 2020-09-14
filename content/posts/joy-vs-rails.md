---
title: Joy vs Rails
date: 2020-09-14
---

# Joy vs Rails

Here are some things that you do in rails and how to do them in [joy](https://joyframework.com):

## Routing

### The rails way:

```ruby
# routes.rb

get '/' => 'home#index'

# app/controllers/home.rb

class HomeController < ApplicationController
  def index; end
end

# app/views/home/index.html.erb

<h1>Home</h1>
```

Just want to point out, that's three different files for one route

### The joy way:

```clojure
; # routes/pages.janet

(route :get "/" :home)

(defn home [request]
  [:h1 "Home"])
```

## Database Migrations

### The rails way

```ruby
class CreatePostsTable < ActiveRecord::Migration[5.1]
  def change
    create_table :posts do |t|
      t.string :title
      t.string :body

      t.timestamps
    end
  end
end
```

### The joy way

```sql
-- up
create table posts (
  id integer primary key,
  title text,
  body text,
  created_at integer not null default(strftime('%s', 'now')),
  updated_at integer
)

-- down
drop table posts
```

I'll give it to rails that although it hides a few things, it definitely makes table definitions readable vs plain sql. And it can also do plain sql migrations as well.

## Code Generation

### The rails way

```sh
rails g scaffold posts title:string body:string
```

This generates **eight** files:

- A database migration file
- A model file
- A controller file
- Five view files
- Edits the `routes.rb` file

### The joy way

Joy doesn't have an equivalent `scaffold` generator yet, so you need to run two commands

```sh
joy create table posts 'title text' 'body text'
joy create controller posts
```

This generates **two** files:

- A database migration file
- A controller file which defines the routes and route functions

## The winner

It's still rails with all of the files and folders to boot.

Rails does a lot of things joy does not:

- cron job schedule file?
- background jobs
- sending AND receiving emails
- websockets

All of these with the exception of websockets can be done:

- keep a cron file in git and apply manually
- spin up another joy server and send requests to it for background jobs
- mailgun

As for websockets, [it's coming](https://github.com/joy-framework/halo/issues/16)
