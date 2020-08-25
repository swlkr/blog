---
title: "SBT Native Packager"
date: 2020-08-25T10:24:27-06:00
---

# Add SBT Native Packager To Your Scala Project

Ever work on a scala project? Ever wanted to turn it into a Docker image
without writing a Dockerfile?

Well then, this short but sweet tutorial has you covered!

First things first, the assumption is that you have an existing scala project.

Next things next, go ahead and:

```sh
touch project/plugins.sbt
```

And add the following line to that newly created `project/plugins.sbt`

```scala
addSbtPlugin("com.typesafe.sbt" % "sbt-native-packager" % "1.7.5")
```

The version will probably have changed by the time you read this, don't forget to use the latest.

You can go ahead and fire up your sbt console if you want, it should download the required dependencies.

The last step is to add the following two lines to your `build.sbt`

```scala
enablePlugins(JavaAppPackaging)
```

and optionally:

```scala
dockerUpdateLatest := true
```

Alright, now that you have all of that ready to go, run this from your shell:

```sh
sbt docker:publishLocal
```

And, assuming your docker cli bits are running, you should be able to verify you now have a docker image:

```sh
docker image ls
```

Happy hacking!
