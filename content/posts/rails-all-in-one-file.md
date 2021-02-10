---
title: Rails all in one file
date: 2021-01-24
---

# Rails all in one file

It's actually not that bad when you put it all together.

```ruby
# Migrations
create_table :todos do |t|
  t.string :name
end

# app/models/todo.rb
class Todo < ApplicationRecord
end

# app/controllers/todos_controller.rb
class TodosController < ApplicationController
  def index
    @todos = Todo.all
  end

  def new
    @todo = Todo.new
  end

  def create
    @todo = Todo.new(todo_params)

    if @todo.save
      redirect_to todos_path
    else
      render :new
    end
  end

  private

  def todo_params
    params.require(:todo).permit(:name)
  end
end

# app/views/new.html.erb
<%= form_for @todo do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>

  <%= link_to "Cancel", todos_path %>
  <%= f.submit "Save" %>
<% end %>

# app/views/index.html.erb
<ul>
  <% @todos.each do |todo| %>
    <li><%= todo.name %></li>
  <% end %>
</ul>

# config/routes.rb
resource :todos, only: [:new, :create, :index]
```

I know the concerns aren't separated, but I mean, you know, it's all required and it's all there.
