ArkhamJS - Flux Framework for ReactJS
=======================

An ES6 Flux library that uses immutability and includes:
- Flux
- Store

[![npm version](https://img.shields.io/npm/v/arkhamjs.svg?style=flat-square)](https://www.npmjs.com/package/arkhamjs)
[![Travis](https://img.shields.io/travis/nitrogenlabs/arkhamjs.svg?style=flat-square)](https://travis-ci.org/nitrogenlabs/arkhamjs)
[![npm downloads](https://img.shields.io/npm/dm/arkhamjs.svg?style=flat-square)](https://www.npmjs.com/package/arkhamjs)
[![Issues](http://img.shields.io/github/issues/nitrogenlabs/arkhamjs.svg?style=flat-square)](https://github.com/nitrogenlabs/arkhamjs/issues)
[![Gitter](https://img.shields.io/gitter/room/NitrgenLabs/arkhamjs.svg?style=flat-square)](https://gitter.im/NitrogenLabs/arkhamjs)
[![MIT license](http://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](http://opensource.org/licenses/MIT)

### Installation

Using [npm](https://www.npmjs.com/):

    $ npm install arkhamjs

###App Usage
Then with a module bundler like [webpack](https://webpack.github.io/) that supports either CommonJS or ES2015 modules, use as you would anything else:

```js
// Using an ES6 transpiler for web apps
import {Flux, Store} from 'arkhamjs';

// not using an ES6 transpiler
var Flux = require('arkhamjs').Flux;
var Store = require('arkhamjs').Store;
```

### How to use

A complete example can be found in the [arkhamjs-skeleton](https://github.com/nitrog7/arkhamjs-skeleton). Below is an example of an action and a store.

**Store:**
```js
import {Flux, Store} from 'arkhamjs';
import {Map} from 'immutable';

class App extends Store {
  initialState() {
    return {
      test: 'default'
    };
  }

  onAction(type, data, state) {
    switch(type) {
      case 'APP_TEST':
        return state.set('test', data.get('demo'));
      case 'APP_RESET':
        return Map(this.initialState());
      default:
        return state;
    }
  }
}

export default Flux.registerStore(App);
```

**Action:**
```js
import {Flux} from 'arkhamjs';

const AppActions = {
  test: (str) => {
    Flux.dispatch({type: 'APP_TEST', demo: str});
  }
};

export default AppActions;
```

**Component:**
```js
import React, {Component} from 'react';
import {Flux} from 'arkhamjs';

// Enable console debugger
Flux.enableDebugger();

export default class AppView extends Component {
  constructor(props) {
    super(props);
    
    // Initial state
    this.state = {
      myTest: ''
    };

    // Bind methods
    this.onAppTest = this.onAppTest.bind(this);
  }
  
  componentWillMount() {
    // Add listeners
    Flux.on('APP_TEST', this.onAppTest);
    
    // Initialize
    AppActions.test('Hello World');
  }

  componentWillUnmount() {
    Flux.off('APP_TEST', this.onAppTest);
  }
  
  onAppTest() {
    // Gets the immutable store
    const myTest = Flux.getStore(['app', 'test'], '');
    
    // Show the output in the console
    console.log('onAppTest::myTest', myTest);
    
    // Set state to re-render component
    this.setState({myTest});
  }
  
  render() {
    return <div>{this.state.myTest}</div>
  }
};

export default AppActions;
```

### API
#### `on(eventType, data)`

Adds an event listener. It is called any time an action is dispatched to Flux, and some part of the state tree may 
potentially have changed. You may then call getStore() to read the current state tree inside the callback.

* [`eventType`] \(*String*): Event to subscribe for store updates.
* [`listener`] \(*Function*): The callback to be invoked any time an action has been dispatched.

#### `off()`

Removes an event listener.

* [`eventType`] \(*String*): Event to unsubscribe.
* [`listener`] \(*Function*): The callback associated with the subscribed event.

#### `dispatch(action)`

Dispatches an Action to all stores

* [`action`] \(*Object*): An action object. The only required property is *type* which will indicate what is called in
the stores, all other properties will be sent to the store within the *data* object.

#### `getStore(name, default)`

Get the state tree. If only a particular store is needed, it can be specified.

* [`name`] \(*String*/*Array*): (optional) A store name. May also use an array to get a nested property value.
* [`default`] \(*String*/*Immutable*): (optional) The default value if undefined. This may be a string or immutable 
object (ie. Map, List, etc.).

#####Returns

An Immutable object or a string.

#### `getClass(name)`

Get the store class object.

* [`name`] \(*String*): The name of the store class object to retrieve.

#####Returns

A store class object.

#### `registerStore(Class)`

Registers the store with Flux.

* [`Class`] \(*Class*): The store class.

#####Returns

A new object from the class. This is usually exported at the end of the store class.

#### `deregisterStore(name)`

Unregisters the store with Flux.

* [`name`] \(*String*): Name of store to remove from Flux.

#### `getSessionData(key)`

Get an object from sessionStorage.

* [`key`] \(*String*): Key of object to retrieve.

#####Returns

An Immutable object or a string.

#### `setSessionData(key, value)`

Save an object to sessionStorage.

* [`key`] \(*String*): Key to reference object.
* [`value`] \(*String|Object|Immutable*): A string or object to save. Immutable objects will be converted to JSON. All 
objects will converted to a string before saving.

#### `delSessionData(key)`

Remove an object from sessionStorage.

* [`key`] \(*String*): Key of object to delete.

#### `getLocalData(key)`

Get an object from localStorage.

* [`key`] \(*String*): Key of object to retrieve.

#####Returns

An Immutable object or a string.

#### `setLocalData(key, value)`

Save an object to localStorage.

* [`key`] \(*String*): Key to reference object.
* [`value`] \(*String|Object|Immutable*): A string or object to save. Immutable objects will be converted to JSON. All 
objects will converted to a string before saving.

#### `delLocalData(key)`

Remove an object from localStorage.

* [`key`] \(*String*): Key of the object to remove.

#### `enableDebugger()`

Turn on the console debugger to display each action call and store changes.

