---
tags:
- java
- java8
- web
- http
- web-sockets
- rx
- streams
comments: true
date: 2015-02-01T00:00:00Z
title: Choosing Java Backend framework in 2015.
url: /2015/02/01/searching_for_new_java_backend/
---

So it's year 2015 and I'm taking a role in a new cloud service, helping to choose the right backend technology,

This is the summary of our requirements:

1. Can quickly come up with some POC.
1. HTTP driven.
1. Native support for Async.
1. Modular.
1. Avoid frameworks that dictates specific code structure.

Here are few options we tried but decided to pivot and seek for alternatives,


## Akka

[Akka](http://www.akka.io) seems very promising, it feels lightweight, highly concurrent and distributed,
Akka is based on actors pattern, an old pattern invented by _Carl Hewitt_ and friends, promoted lately by Erlang,

We tried to create a POC with Akka, in conjuction with their new [Akka Streams](http://doc.akka.io/docs/akka-stream-and-http-experimental/current) (which seems to replace [Spray](http://www.spray.io)), both felt like a complete solution, but we decided to pivot out of it, I think I can summarize it in one sentence.

**Although Akka seems to have support for Java, it's just not a java project,
Akka is written in _Scala_, and exposes Java APIs via bindings layers, I felt like its community is Scala oriented debugging code of Scala is something tough for people that plans to stick in the Java domain.**

## Vert.x

[Vert.x](http://www.vertx.io) is similar to NodeJs, written in Java,
It is Netty based and works in a non blocking mode,

Vertx 3 seems to be very promising we found some advantages of it,

1. We quickly could create a web app using [Apex](https://github.com/vert-x3/vertx-apex).
1. Almost no configuration and deployment code is required to see real results.
1. Completely written in Java (which was a big releaf after Akka)
1. Scale out by levaraging an internal even bus.


But we decided to pivot again, but why?!
because Vert.x makes certain irreversible decisions about how applications should be written, and working with the event bus was a nightmare, we tried to use [Vertx Service Factory](https://github.com/vert-x3/vertx-service-factory) which eliminates most of the boilerplate code required to perform actions over the event bus, **but it restrictions of passing only JsonObject and JsonArray frustrated us**,
We found ourself spending too much time on convertions between Vertx's JsonObjects and application / core services models.

## What now?

So what now? we'r still seeking for a better solution that hopefully will satisfy our needs.