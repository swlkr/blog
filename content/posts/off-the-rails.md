---
title: Off the Rails
draft: true
date: 2021-01-25
---

# Off the Rails

Let's do a thought experiment.

Before I go any further, think about what's happening underneath rails' and most other web frameworks' abstractions, and http request comes in and it gets turned into one or more sql queries, the results of which then get mixed with html and returned in an html response, or you get a simple redirect. So it looksl ike this:

1. HTTP Request
2. SQL Queries
3. HTML + SQL Query Results or Redirect

That's pretty much it, there could be a few more steps, like background jobs or direct file uploads or calling out to other apis or what have you, but for the majority of most "information systems", that's it. All web frameworks, including rails are abstractions on top of this. I've been trying to make a decent web framework in clojure and now janet or a little while now, so the rest of this post is my attempt to "conventionize" my thinking.
