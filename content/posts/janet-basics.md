---
title: Janet Basics
date: 2020-07-02
---

# Janet Basics

```clojure
; # A pound sign (or hashtag) starts a one-line comment.
; # I'm using clojure's ; comments because chroma doesn't support janet
```

```clojure
(comment
  Wrapping something in the
  comment form makes a multiline comment)
```

## Variables and flow control

```clojure
(def num 42) ; # All numbers are double precision floating point numbers

"A few more numbers"

(def num1 10_000) ; # you can use underscores as decimal places, like ruby

(def s "the good place") ; # Immutable strings
(def s `Backticks
        start/end multine strings`) ; # backticks for multiline strings
(def t nil) ; # nil is a thing

; # loops can be done with either map, loop or while
(while (< i 50)
  (++ i))

; # If clauses:
(if (> num 40)
  (print "over 40")
  (do
    (if (not= s "the good place")
      (print "not over 40")       ; # you don't need a second form necessarily
    (var this-is-local 5))))      ; # variables are lexically scoped by default

; # How to make a variable global (to a file)
; # outside of any do's or any loops or anything
(var x "im global")

; # String concatentation uses the (string) or (print) forms

(string "winter is coming " "quickly") ; # => "winter is coming quickly"
(print "winter is coming" "quickly") ; # => "winter is comingquickly"

; # Undefined variables throw errors.
; # This is an error:
(def x an-unknown-variable) ; # => compile error: unknown symbol an-unknown-variable while compiling repl

(def a-bool-value false)

; # nil and false are falsy, 0 and "" are not!
(when (not aBoolValue) (print "twas false"))

; # 'or' and 'and' are short-circuited.
(def ans (or (and a-bool-value "yes") "no")) ; # => "no"

(var a-sum 0)
(for i 1 100 ; # the range does NOT include both ends
  (+= a-sum i))

; # Another loop construct:
(var sum 0)
(loop [i :range [0 10] :when (even? i)] (+= sum i))
(print sum) ; # prints 20
```

## References

Check out [the official docs](https://janet-lang.org/docs/index.html) for more info, and be sure to check out the [gitter channel](https://gitter.im/janet-language/community) if you have any questions or just want to say hi.

There's also [janet docs](https://janetdocs.com) which is a very unofficial community docs site I put together with the [joy web framework](https://joyframework.com) that I also put together.

I'll update this post with more examples in the vein of [https://learnxinyminutes.com](https://learnxinyminutes.com) eventually.
