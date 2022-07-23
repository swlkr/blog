---
title: Thoughts on learning go
date: 2022-07-23
---

# Thoughts on learning go

I wanted to write down a few things related to jumping back into learning new languages, but specifically go. I evaluated a few statically typed languages in the C family for my foray back into static typing after clojure, janet, ruby, and javascript and decided on go since it seems to be purpose built for web development.

Things I like about go:

- Simplicity
- Flexible static types (interface{})
- Huge standard library
- Tooling (LSP + vim-go)
- AOT compiles to single binary
- It’s fast!

Things I don’t like:

- Simplicity
- Static Typing

## Simplicity

I really appreciate the focus on simplicity in the language, not too many sigils (<>&!), feels approachable/similar to dynamic typing. There are no macros, this makes me sad. There is reflection for stuff like database access, but I mean I might as well use a dynamic language. Still looking into code generation.

## Flexible static types

They have generics now which is great, but I mean the fact that you can declare a function parameter as interface{} and pass anything you want. This works out well for view data.

## Huge standard library

It’s massive. It does everything you could ever want in web development aside from write your app for you. The thing has an http server built in!

## Tooling

I don’t use IDEs, I use neovim and language servers. So for my setup, the tooling is incredible! The static analyzer is fast, autocomplete is great, integrated docs are amazing, built in gofmt on every save. So nice.

## AOT compiles to single binary

This was what drew me back to statically typed languages in the first place. I’m tired of docker or trying to vendor dependencies in ruby. I just want to compile everything to one binary, zip a folder and send it on its way to my server. As an indie hacker, my CD deployment pipeline is just a script on my machine.

## It’s fast!

Coming from rails, it amazes me to see microsecond responses on localhost even when I add sessions and csrf middleware.

## Static typing

Fast compile times are nice but Go's static typing feels like a glorified typo catcher.

# Conclusion

Even with all of the trade offs made with a language like go, I find myself very productive and producing remarkably small binaries and using remarkably less memory than rails, which to me, is a net win at the cost of development speed.
