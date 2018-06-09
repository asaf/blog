---
title: Accessing EmberJS Controller from different scopes
date: 2013-07-08T00:00:00Z
tags:
- emberjs
- javascript
- mvc
url: /2013/07/08/accessing-emberjs-controller-from-different-scopes/
---

Sometimes you want to use one _ember_ controller from another,
This simply can be done by "asking" ember access to the other controller:

``` javascript
App.PostController = Ember.ArrayController.extend({
    ...
})

App.CommentsController = Ember.ArrayController.extend({
  needs: "post"
});
```

Then you can simply use the _Post_ controller in _Comments_'s template:


{% raw %}
``` html
<!-- use comments template -->
{{ controllers.post }}
```
{% endraw %}


This works pretty nice, especially when you have nested routes (you surely want to display some data of the post when you are in the post's comments context.),

But what if you need to access a controller outside of Ember's scope?

For instance, you may have a websocket listening to a certain top level event and would like to update a certian controller when data is pushed,

You can use the container lookup to retrieve a controller instance:

``` javascript
// Somewhere out of Ember`s scope
ss.event.on('incomingComment', function(comment) {
    commentsController = App.__container__.lookup('controller:Comments')
    commentsController.pushObject(comment)
})
```
