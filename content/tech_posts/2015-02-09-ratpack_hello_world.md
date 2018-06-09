---
tags:
- java
- java8
- web
- http
- web-sockets
- rx
- streams
- ratpack
comments: true
date: 2015-02-09T00:00:00Z
title: Hello world using Ratpack.
url: /2015/02/09/ratpack_hello_world/
---

## About Ratpack

[Ratpack](http://www.ratpack.io) is a set of Java libraries that facilitate fast, efficient, evolvable and well tested HTTP applications built on _Netty_ event driven networking engine,

After crawling into its source code, I find it simple and easy to use and it feels right, why? because:

1. It's super simple, its core has very limited number of [dependencies](https://github.com/ratpack/ratpack/blob/master/ratpack-core/ratpack-core.gradle)
1. Has out of the box extensions such as Guice, Jackson, etc, but nothing dictates you using them thanks to Guice modular architecture.
1. Compatible with Java8 lambda syntax
1. Testing is very easy.

Code can be written in _Groovy_ or _Java_, and since java8 lambda syntax is so pretty, I find it suitable for the Ratpack handlers architecture quit well.

So here's a simple hello world using _Ratpack_ and Java8,

## The build file (Gradle)

While used to Maven, It feels like Ratpack folks like Gradle, so I said why not use it?

Here's a simple _build.gradle_ file:


``` groovy
buildscript {
  repositories {
    jcenter()
  }
  dependencies {
    classpath "io.ratpack:ratpack-gradle:0.9.13"
  }
}

apply plugin: "io.ratpack.ratpack-java"
apply plugin: "idea"

dependencies {
  //Ratpack uses slf4j for logging
  runtime "org.slf4j:slf4j-simple:1.7.5"
}

//we need this since 0.9.13 is based on snapshot version of Netty
repositories {
  jcenter()
    maven {
      url "http://clinker.netty.io/nexus/content/repositories/snapshots"
    }
}
```

Create basic directories
``` bash
mkdir -p src/main/java
mkdir -p src/ratpack
```

Run in the command like `gradle idea` to create Intellij project:
``` bash
gradle idea
```

One of the things I mostly like about _Ratpack_ is that it has no real notion of a "container", you can simply start it a plain _main_ method just like that:

``` java
import ratpack.server.RatpackServer;
import ratpack.server.ServerConfig;

/**
 */
public class Server {
    public static void main(String[] args) throws Exception {
        RatpackServer server = RatpackServer.of(b -> b
                        .serverConfig(ServerConfig.embedded()) //default config
                        .registryOf(r -> r.add(String.class, "world"))  // registry of supporting objects - optional
                        .handlers(chain -> chain                    // request handlers - required
                                        .get("hello", ctx -> ctx.render(ctx.get(String.class) + " !"))
                        )
        );

        server.start();
    }
}
```

Server will start with on some available port and will print:
`Ratpack started for http://localhost:65478`

now hit in the browser `http://localhost:65478/hello` ! :)
