---
tags:
- react
- js
- html5
- facebook
- systemjs
- jspm
comments: true
date: 2015-06-19T00:00:00Z
title: Elegant way to use React with ES6/ES7 features.
url: /2015/06/19/elegant-way-to-use-react-with-es6-slash-es7-features/
---

## React, JSX, ES6.

So you want React, JSX the markup and write ES6/7 code style, but setting a working
environment may be pain in the ass,

More to say, Facebook announced that its JSTransformer and react-tools going to be [deprecated](https://facebook.github.io/react/blog/2015/06/12/deprecating-jstransform-and-react-tools.html),

It seems like it's starting to get confusing how to set a full working environment
with JSX transformation and ES6/ES7 support.

While React 0.13.0-beta-1 announced great support for ES6, it still unclear how
to build a full working environment that supports ES6 either,

You can find multiple boilerplate projects at [github](http://www.github.com) using
WebPack, Browserify and other module loader tools available nowadays but I found all
of them unclean, full of build scripts and things that just make things more complicated,

After doing some research, I feel like the best approach would be to go with [JSPM](http://jspm.io/) repository that is based on [SystemJs](https://github.com/systemjs/systemjs) dynamic module loader.

The chosen tools:

1. SytemJs - It is a Universal dynamic module loader, it supports AMD, CommonJS
but the best of it is its native support for ES6 modules, with native support for
Babel as its transpiler to ES5, it also uses [JSPM](http://jspm.io) which supports
NPM and Github repos such as Bower does, so you can enjoy packages from both worlds.
1. [Babel](https://babeljs.io) as the ES6 transpiler, this transpiler is responsible
to translate ES6 coding to ES5 as ES6 is not supported yet by most browsers, Babel is
also supported by Facebook and provides JSX native transformation.



Enough said, here's some code, we'll start with empty folder!

## Setting a clean working environment

### Pre requisites

Lets install pre requisites:

``` bash
npm install -g jspm
npm install -g http-server
```

### Initializing Project

Create an empty folder and run

``` bash
jspm init
```

You can choose default answers for all questions if not sure,

This will create a `package.json` and a `config.js` file required for SystemJs
to work properly.

Now lets tweak `config.js` a bit to support ES7 so you can enjoy ES7+ property
initializers and other ES7 cool features

``` javascript
"babelOptions": {
  blacklist: [],
  stage: 0,
  "optional": [
    "runtime"
  ]
},
```

`blacklist: []` tells Babel not to ignore JSX transformation as by default it's disabled,
`stage: 0` tells Babel that we want to use experimental ES7 syntax.


### Creating an index.html file

Lets create our index file as every web app that respect itself has one!

Simply create `index.html` in the root folder with your favorite editor

```javascript
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Title Here</title>
    </head>
    <body>
        <!-- build:js -->
        <script src="jspm_packages/system.js"></script>
        <script src="config.js"></script>
        <script>
            System.import('app/main');
        </script>
        <!-- endbuild -->
    </body>
</html>
```

See how clean and elegant is it? we just load `system.js` as our dynamic module loader
and the generated `config.js` that holds our `SystemJs` configuration.

Then we import `app/main` as our first ES6 module that serves the application.


### Create a simple React based class.

Thanks to SystemJs, we can now fully write application modules, lets create our
`main` application entry point

Create a file `app/main.js` with the following content:

``` javascript
import React from 'react';
import App from './app';

React.render(<App/>, document.body);
```

This just import `React` and renders our `App` component.

Her eis the `app/app.js` file content:

``` javascript
import React from 'react';
var {Component} = React;

export default class MyApp extends Component {
    static propTypes = {
       flux: React.PropTypes.object.isRequired
   }

    render () {
        return (
            <div>Hello</div>
        )
    }
}
```

Now run `http-server` in your folder,

hitting http://localhost:8080 should display *Hello* :)


## Summary

The combination of React, JSX transformation via Babel, and ES6/7 support via
SystemJS provides a great clean and elegant way to write React code without the
overhead of build systems and ugly scripts around.
