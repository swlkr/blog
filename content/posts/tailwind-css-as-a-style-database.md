---
title: Tailwind CSS as a Style Database
date: 2020-10-05
---

# Tailwind CSS as a Style Database

I was messing around with how to make web apps more quickly, like always, and I found a way to use [hiccup](https://github.com/joy-framework/joy/blob/master/docs/html-rendering.md), [janet bindings](https://janet-lang.org/docs/bindings.html) and [tailwind](https://tailwindcss.com) to do it.

## How does it work?

There are two janet files:

1. `html.janet`
2. `classes.janet`

`classes.janet` is responsible for taking the styles from the `html.janet` file, grabbing them from the `tailwind.all.min.css` file, taking only the styles that are in use and making a smaller `tailwind.min.css` file.

Here's what `html.janet` looks like:

```clojure
; # html.janet

(defn class [el & args]
  (keyword el (splice (map |(string "." $) args))))

(def h1 :h1.text-5xl.text-gray-800)
(def h2 :h2.text-3xl.text-gray-800)
(def h3 :h3.text-xl.text-gray-800)
```

In reality there are more styles than just three but this is a blog post to show it off. So this is hiccup, or more specifically, janet keywords that represent html:

```clojure
(use joy)

(def h1 :h1.text-5xl.text-gray-800)

(html [h1 "hello"]) ; # => "<h1 class="text-5xl text-gray-800">hello</h1>"
```

So each binding represents an html element with a set of tailwind classes. A cool side effect is if you try to use an element that doesn't exist, you get a compiler error. Of course you can always use a keyword directly in your html if you don't need any tailwind styles.

Moving on to `classes.janet`:

```clojure
; # classes.janet

(import ./html)

(def classes (->> (all-bindings (curenv) true)
                  (filter |(string/has-prefix? "html/" $))
                  (map eval)
                  (filter keyword?)
                  (mapcat |(->> (string/split "." $) (drop 1)))
                  (distinct)
                  (sort)
                  (reverse)
                  (map |(string/replace-all `/` `\/` $))
                  (map |(string/replace-all `:` `\:` $))))

(def tailwind-min-css (slurp "public/tailwind.all.min.css"))

(def normalize-css (string/slice tailwind-min-css 0 (inc (string/find "}.container" tailwind-min-css))))

(defn css-class-peg [classes]
  (peg/compile ~{:main (% (any (+ (* (<- :class) (<- (constant "}"))) 1)))
                 :class (* "." (+ (splice ,classes)) (+ "{" ">") (some (if-not "}" 1)))}))

(as-> (css-class-peg classes) ?
      (peg/match ? tailwind-min-css)
      (first ?)
      (string normalize-css ?)
      (spit "public/tailwind.min.css" ?))
```

This code does three things:

1. import `html.janet` and read all of the bindings that start with `html/`
2. read `tailwind.all.min.css`, strip out the prefix (normalize-ish.css part) and grab only the classes used in `html.janet` with a PEG
3. output a new `tailwind.min.css` file with *just* the classes used in `html.janet`

No node, npm or purgecss required!

It's pretty rough at this point. I've only used this in one project so far, but it works great! This may make it into future versions of joy or maybe just a library for working with tailwind (or another atomic css framework).