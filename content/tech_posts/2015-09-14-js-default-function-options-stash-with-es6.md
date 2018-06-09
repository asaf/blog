---
tags:
- es6
- js
- javascript
- destructuring
- tips
comments: true
date: 2015-09-14T00:00:00Z
title: Default function options stash with ES6 destructuring assignment.
url: /2015/09/14/js-default-function-options-stash-with-es6/
---

ES6 opens up a clean approach to define function options stash thanks to the destructuring assignment syntax,

Assuming we want options but let the user overrides it, we can declare our function that way:

function hello(param1, {debug = true, whatever = false} = {}) {
}


The advantages are, clearly there is no code in the body function to achieve default options & user overrides.
