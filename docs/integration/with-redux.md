# With Redux


## Demo and example with Redux

* [https://stackblitz.com/edit/react-redux-router5](https://stackblitz.com/edit/react-redux-router5)


## How to use

You have two ways to use redux-router5, depending on how you want to navigate:
- Using the router5 plugin (named `reduxPlugin`) 
- Using the redux middleware (named `router5Middleware`)

In both cases, __use the provided reducer (`router5Reducer`).__


## Using the router plugin

If you choose to not use the middleware, you need to add `reduxPlugin` to your router. The plugin simply syncs the router state with redux. To navigate, you will need to invoke `router.navigate`. If you use React, you can use `BaseLink` from `react-router5`.

```js
import { reduxPlugin } from 'redux-router5';

// You need a router instance and a store instance
router.usePlugin(reduxPlugin(store.dispatch));
```


## Using the redux middleware

The redux middleware automatically adds the redux plugin to the provided router instance. It will convert a set of redux actions to routing instructions. The available action creators are:

- `navigateTo(routeName, routeParams = {}, routeOptions = {})`
- `cancelTransition()`
- `clearErrors()`
- `canActivate(routeName, true | false)`
- `canDeactivate(routeName, true | false)`

```javascript
import { actions } from 'redux-router5'
```

Use `router5Middleware` alongside your other middlewares:

```javascript
import { createStore, applyMiddleware } from 'redux';
import { router5Middleware } from 'redux-router5';

const createStoreWithMiddleware = applyMiddleware(
    router5Middleware(router)
)(createStore)
```


## Reducer

This packages exposes a reducer (`router5Reducer`) that you can add to your application. It contains four properties:
- `route`
- `previousRoute`
- `transitionRoute` (the current transitioning route)
- `transitionError` (the last error which occured)

```js
import { combineReducers } from 'redux'
import { router5Reducer } from 'redux-router5'

const reducers = combineReducers({
    router: router5Reducer,
    // ...add your other reducers
})
```


## Route node selector

{% hint %}
In order to use routeNodeSelector efficiently, you need react-redux >= 4.4.0 to be able to perform per component instance memoization.__
{% endhint %}

`routeNodeSelector` is a selector creator designed to be used on a route node and works with `connect` higher-order component from `react-redux`.

If your routes are nested, you'll have a few route nodes in your application. On each route change, not all components need to be re-rendered. `routeNodeSelector` will only output a new state value if the provided node is concerned by a route change.


```javascript
import { connect } from 'react-redux';
import { routeNodeSelector } from 'redux-router5';
import { Home, About, Contact } from './components';
import { startsWithSegment } from 'router5-helpers';

function Root({ route }) {
    const { params, name } = route;
    const testRoute = startsWithSegment(name);

    if (testRoute('home')) {
        return <Home params={ params } />;
    } else if (testRoute('about')) {
        return <About params={ params } />;
    } else if (testRoute('contact')) {
        return <Contact params={ params } />;
    }

    return null
}

export default connect(routeNodeSelector(''))(Root);
```

Using `routeNodeSelector` with other connect properties:

```js
export default connect(state => {
    const selector = routeNodeSelector('');

    return (state) => ({
        a: state.a,
        b: state.b,
        ...selector(state)
    })
)(Root);
```


## With immutable-js

If you are using [immutable-js](https://github.com/facebook/immutable-js) and [redux-immutable](https://github.com/gajus/redux-immutable) simply use the reducer from 'redux-router5/immutable/reducer' 

```javascript
import router5Reducer from 'redux-router5/immutable/reducer';
```