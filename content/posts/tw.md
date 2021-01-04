---
title: tw
date: 2021-01-04
---

# tw

Just wanted to give a little update on my tailwind experiments from a couple of months ago: https://swlkr.com/posts/tailwind-css-as-a-style-database/

I've streamlined it quite a bit and store the classes in memory instead of a separate file. So you can write the tailwind styles inline or wherever, just be sure to call two (or three functions).

Let's say you're writing some [janet-html](https://github.com/brandonchartier/janet-html) and you want to use tailwind to style some things. Normally you would either do two things:

1. Use node and some css tool like purgeCSS or postCSS or whatever
2. Just include the tailwind.min.css in your `<head>` and deal with the huge size

Well, for janet projects that don't want to add a ton of complexity, 1. is out. Number 2. is super simple but minified tailwind is huge, like 2 MB huge and you don't get a ton of variants and things. I've tried turning on most of the variants and it winds up being 5 MB!! So that's kind of a non-starter.

This leads me to why I wrote [tw](https://github.com/swlkr/tw), you can get a per-page stylesheet so small that you don't even need a separate css file! You can stick the output right into a `<style>` tag in the `<head>` and that's that.

Here's how it works:

Let's take some fictitious [osprey](https://github.com/swlkr/osprey) code:

```clojure
(import osprey :prefix "")
(import tw)

(def home "/")
(def /hello "/hello")

(tw/url home)
(GET home
  [:html
   [:head
    [:style (html/unsafe (tw/style home))]]
   [:body {:class (tw/class "container")}
    [:button {:href /hello :class (tw/class "p-4 transition transform hover:scale-110 rounded-md bg-gray-800 text-white dark:bg-white dark:text-gray-800")}
      "I'm a button"]]])

(tw/url /hello)
(GET /hello
  [:html
   [:head
    [:style (html/unsafe (tw/style /hello))]]
   [:body {:class (tw/class "container")}
    [:a {:href home :class (tw/class "hover:underline text-gray-800")}]]])

(tw/tailwind.min.css "tailwind.min.css")

(server 9001)
```

There are a few things going on here that aren't obvious, so let me go over them one by one.

## tw/url

This function is called before you call `tw/class` and it sets the current url that you want to target with just the styles you need.

## tw/style

This function is what outputs the minified tailwind styles for a given page

## tw/class

This is a macro that looks like it runs at run time when a request gets sent to the server but it really runs at startup before any requests are received. It uses the scope (if any) to group the class names by url.

## tw/tailwind.min.css

This takes one argument, the path to your minified tailwind css file. This only works if the tailwind css file is minified, so that's important.

[Here's the repo I use](https://github.com/swlkr/tailwind-config) to generate a pretty large tailwind.min.css with dark mode, focus and hover variants enabled.

If you clone and already use vagrant you can get a `tailwind.min.css` file by running:

```sh
vagrant up
vagrant ssh
cd /vagrant
npm run tailwind
npm run build
```

Then copy `output.css` to your project and that's it, `tw` will take over!

There are a few quality of life improvements you can make if you don't want to keep typing `tw/class` over and over again. I usually wind up settling on common styles pretty early on and I put those in macros like so:

```clojure
(import osprey :prefix "")
(import tw)

; # these styles will be copied across all pages
(tw/url "")
(def .container (tw/class "container"))

; # these are just strings, reuse will come from
; # tw/class, tw/url since they aren't used
; # on all pages
(def link "hover:underline text-gray-800")
(def btn "p-4 transition transform hover:scale-110 rounded-md bg-gray-800 text-white dark:bg-white dark:text-gray-800")

(def home "/")
(def /hello "/hello")

(tw/url home)
(GET home
     [:html
      [:head
       [:style (html/unsafe (tw/style home))]]
      [:body {:class .container}
        [:button {:href /hello :class (tw/class btn)}
          "I'm a button"]]])

(tw/url /hello)
(GET /hello
     [:html
      [:head
       [:style (html/unsafe (tw/style /hello))]]
      [:body {:class .container}
       [:a {:href home :class (tw/class link)}]]])

(tw/tailwind.min.css "tailwind.min.css")

(server 9001)
```

Everything that is used across multiple pages lives in the global "tw/url" this way and with janet's flexible variable names you can make that `janet-html` look really close to css with the `.` prefix for classes.
