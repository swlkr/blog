---
title: The future of user interfaces
date: 2020-03-18
---

# The future of user interfaces

How do we really want to interact with computers? I don’t actually want to tap on a screen, I actually want to...

- write a blog post
- record music
- write some code
- watch a video
- listen to a podcast

So how do I do these things today? I figure out an app to do the thing and then I remember the icon and the name and tap it. Then I navigate to the function I want in that app across a multitude of different user interfaces.

Is this what I want? Not really. I want to tell the computer what information I need or what I need to accomplish and the move on with my life. I want something like a command line but with bits of UI mixed in along with a myriad of APIs. I don’t think the diversity of applications should happen at the UI layer, I think it should happen at the data layer. Of course, who would willingly give up the ability to control what a user sees when they interact with their data? No one. So we live with proliferation of complex, confusing interfaces.

There may be a way to have this command line GUI but it’s going to have to be open source and cross platform, similar to http/web browsers themselves or google search but better and by cross platform I mean voice activated as well.

Here’s a very simple concrete example that google already does well:

Just typing “di” probably brings up dictionary or “def” -> definition or you can type in any word and get the definition right there. Simple examples like that don’t really cost money and no one cares about multiple choices of dictionary. The vast majority of google searches are limited to information retrieval, what I'm talking about is something like command execution.

Take sending an email newsletter for example:

Given our magic command line GUI, you type "send email" and a few things may happen next

1. You may see a form with common email fields (to, from, subject, body) and possibly be able to upload or type in a list of people you’d like to send to
2. You’d get a list of choices of email newsletter providers with prices per # of emails to send
3. Finally you can opt to save your preference for next time

This future isn't impossible, and I can easily imagine the a "SaaS google" looking something like this and pushing the innovation to the data/API layer where quite a few data people will offer API integration at lower and lower price points as competition heats up.

The command line GUI is definitely something that seems way more plausible than it did even 5 years ago with the rise of native apps and js-heavy frontend applications in the browser backed by REST and graphql APIs.

Inspired by:

[https://blog.repl.it/clui](https://blog.repl.it/clui)
