---
title: One File Websites With Janet
date: 2021-02-03
---

# One File Websites with Janet

A weird, reoccurring theme on this blog and in my life is this idea of a one file website. I don't know why I care so much, but I do. I even wrote a whole [web thing](https://github.com/swlkr/osprey) on top of [janet](https://janet-lang.org) to do it! I'll cut to the chase and give you a quick preview of what it looks like and then go over how to install it and get started on your own.

```clojure
(use osprey)

(GET "/" "hello")

(server 9001)
```

That's it, that's a whole website, three lines of code, I don't believe it can get any terser than this, but I'd love to be proven wrong.

If you like what you see here, and want to see if it actually works, keep reading.

## Step 1: Installation

The first step on your one file website journey is to install janet.

```sh
brew install janet
```

I use homebrew on macOS and linux and you should too!

## Step 2: Creating the one file

Now that you have that going, save the code above to a file with a `.janet` extension with your terminal, go ahead and copy the lines you see here and paste them in that terminal:

```sh
touch main.janet &&
echo '(use osprey)\n\n(GET "/" "hello")\n\n(server 9001)' > main.janet &&
janet main.janet
```

The server should start right up on port 9001!

## Step 3: Testing it out

Open up another terminal and test it out with `curl`:

```sh
curl localhost:9001
```

You should see hello! It's working!

That's the very basics of a one file website with janet. Things can get quite a bit crazier but I'll save that for another post.
