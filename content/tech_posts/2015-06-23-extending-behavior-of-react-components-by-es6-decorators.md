---
tags:
- react
- js
- html5
- facebook
- es6
- decorators
- annotations
- higher-order-components
comments: true
date: 2015-06-23T00:00:00Z
title: Extending behavior of React Components by ES6 Decorators & Higher Order Components
url: /2015/06/23/extending-behavior-of-react-components-by-es6-decorators/
---

## About Mixins & Higher Order Components

Sometimes it makes sense to extends a behavior of a component, while [mixins](https://facebook.github.io/react/docs/reusable-components.html#mixins) are still alive, they'r [not recommended](https://twitter.com/sebmarkbage/status/571389309586051072) anylonger, use [Higher Order Components](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750) instead.


So instead of doing this:

```js
function StoreMixin(...stores) {
  var Mixin = {
    getInitialState() {
      return this.getStateFromStores(this.props);
    },
    ...
  }
}

var UserProfilePage = React.createClass({
  mixins: [StoreMixin(UserStore)],
  propTypes: {
    userId: PropTypes.number.isRequired
  },
  ...
})
```

You can do that:

```js
function connectToStores(Component, stores, getStateFromStores) {
  const StoreConnection = React.createClass({
    getInitialState() {
      return getStateFromStores(this.props);
    },
    ...
    render() {
      return <Component {...this.props} {...this.state} />;
    }
  })
}
```

We just wrap the component and passes some additional props to it, it is a clean and generic solution for extending
components without merging behaviors being implemented by React's mixin merge logic.

You can read more about _Higher Order Components_ [here](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750)

## Extending via ES6 Decorators

Sometimes it makes sense to extends a component without the need to wrap it manually, this technique is common in different [frameworks](https://github.com/gaearon/redux/blob/master/src/components/createConnectDecorator.js) to define components less verbosely when extra functionality is demanded.

Here is a short example how it works,
Lets extends the [context](https://www.tildedave.com/2014/11/15/introduction-to-contexts-in-react-js.html) of our component hierarchy with a `print` function, so we use it in our `render` method:

```js
class MyPage extends Component {
    static contextTypes = contextTypes;
    render() {
        return(
            <div>{this.context.print('Hello World')}</div>
        )
    }
}
```

Assuming `MyPage` is a child component of `App`, here is how our `App` component looks like:

```js
var contextTypes = {
    print: PropTypes.func.isRequired
}

var myContext = {
    print: (m) => (m)
}

@context(contextTypes, myContext)
export default class App extends Component {
    render() {
        return (
            <MyPage/>
        )
    }
}
```

And here is the code of the _decorator_:

```js
export default function context(contextTypes, context) {

    return function (DecoratedComponent) {
        return class {
            static childContextTypes = contextTypes;
            getChildContext() {
              return context;
            }
            render() {
              return (
                <DecoratedComponent {...this.props} />
              );
            }
        }
    }
}
```

The _Decorator_ expects an expression and invokes the function with the given parameters and must return a function that expects the annotated component as a parameter, then we just return a `class` with some context the _wraps_ our component.

You can read more about [Decorators](https://github.com/wycats/javascript-decorators) here.