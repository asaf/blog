---
tags:
- Q
- promises
- async
- nodejs
- coffeescript
comments: true
date: 2013-07-09T00:00:00Z
title: Q promises with CoffeeScript
url: /2013/07/09/q-promises-with-coffeescript/
---

If you are using NodeJS (or for some other reasons you need to do many async invocations), then you probably came across ["https://github.com/kriskowal/q"](Q Promises).

Using Q with CoffeeScript is great as code blocks are much more organized and looks cleaner.

Here's an example:


Defining promises with CoffeeScript:

``` coffeescript
q = require 'q'

exports.hello = () ->
  d = q.defer()
  d.resolve 'hello'
  d.promise

exports.world = () ->
  d = q.defer()
  d.resolve 'world'
  d.promise

exports.die = () ->
  d = q.defer()
  d.reject 'bye world'
  d.promise
```


And here are Mocha sample of Q propagations and error handling

``` coffeescript
assert = require 'assert',
promises = require './promises'

describe('Promises', () ->
  it 'Simple', (done) ->
    promises.die().then(
      (val) =>
        #handle val
      (err) =>
        assert.equal err, 'bye world'
        done()
    )

  it 'Test Promises Propagation', (done) ->
    promises.hello().then(
      (val) =>
        assert.equal val, 'hello'
        promises.world()
    ).then((val) =>
        assert.equal val, 'world'
        done()
    )

  it 'Test promises propagation with error', (done) ->
    promises.hello().then(
      (val) =>
        assert.equal val, 'hello'
        promises.die()
    ).then((val) =>
        promises.world()
    ).then(
      (val) =>
        #we wont get here
      (err) =>
        assert.equal err, 'bye world'
        promises.world()
    ).then((val) =>
      assert.equal val, 'world'
      done()
    )
)
```