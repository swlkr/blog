---
title: Reload roda with zeitwerk on vagrant
date: 2021-04-05
---

# Reload roda with zeitwerk on vagrant

This is for anyone who is strugging with reloading roda with zeitwerk, listen and vagrant.

```ruby
# config.ru
require "roda"
require "zeitwerk"
require "listen"

loader = Zeitwerk::Loader.new
loader.push_dir(__dir__)
loader.enable_reloading
loader.setup

Listen.to(__dir__, only: /\.rb$/, force_polling: true) { loader.reload }.start

run ->(env) {
  loader.reload
  App.call(env)
}
```

This assumes an app.rb file with `class App < Roda`

You can run this with `rackup` in that directory, override port and host like this:

Override port and host with:

`rackup -p 9001 -o 0.0.0.0`
