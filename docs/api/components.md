# Components

## React
[Website](https://facebook.github.io/react/)

**NPM**

`npm install react react-dom babel-preset-react --save`

**YARN**

`yarn add react react-dom babel-preset-react`

```js
import React from 'react'
import {render} from 'react-dom'
import {Controller} from 'cerebral'
import {Container} from 'cerebral/react'
import App from './App'

const controller = Controller({
  state: {
    foo: 'bar'
  }
})

render((
  <Container controller={controller}>
    <App />
  </Container>
), document.querySelector('#app'))
```

```js
import React from 'react'
import {connect} from 'cerebral/react'
import {state, signal} from 'cerebral/tags'

// Stateless
export default connect({
  foo: state`foo`,
  click: signal`clicked`
},
  function MyComponent ({foo, click}) {
    return <div onClick={() => click()}>{foo}</div>
  }
)

// Stateful
export default connect({
  foo: state`foo`,
  click: signal`clicked`
},
  class MyComponent extends React.Component {
    render () {
      return <div onClick={() => this.props.click()}>{this.props.foo}</div>
    }
  }
)
```

You can add an additional function to connect that gives you full control of properties of the component and dependencies. The returned object from this function will be the exact props passed into the component.

```js
import React from 'react'
import {connect} from 'cerebral/react'
import {signal, state} from 'cerebral/tags'

export default connect({
  foo: signal`app.foo`,
  clicked: signal`app.somethingClicked`
}, (dependencyProps, ownProps, resolve) => {
  return {}
},
  function App(props) {

  }
)
```

**dependencyProps** are the props you connected.

**props** are the props passed into the component by the parent.

**resolve** allows you to resolve computed etc., just like resolve in actions.

## Inferno
[Website](http://infernojs.org/)

**NPM**

`npm install inferno inferno-component inferno-create-element babel-plugin-inferno --save`

**YARN**

`yarn add inferno inferno-component inferno-create-element babel-plugin-inferno`

```js
import Inferno from 'react'
import {Controller} from 'cerebral'
import {Container} from 'cerebral/inferno'
import App from './App'

const controller = Controller({
  state: {
    foo: 'bar'
  }
})

Inferno.render((
  <Container controller={controller}>
    <App />
  </Container>
), document.querySelector('#app'))
```

```js
import Inferno from 'inferno'
import Component from 'inferno-component'
import {connect} from 'cerebral/inferno'
import {state, signal} from 'cerebral/tags'

// Stateless
export default connect({
  foo: state`foo`,
  click: signal`clicked`
},
  function MyComponent ({foo, click}) {
    return <div onClick={() => click()}>{foo}</div>
  }
)

// Stateful
export default connect({
  foo: state`foo`,
  click: signal`clicked`
},
  class MyComponent extends Component {
    render () {
      return <div onClick={() => this.props.click()}>{this.props.foo}</div>
    }
  }
)
```

## Preact (BETA)
[Website](https://github.com/developit/preact)

**NPM**

`npm install preact babel-plugin-transform-react-jsx --save`

**YARN**

`yarn add preact babel-plugin-transform-react-jsx`

```js
import {h, render} from 'preact'
import {Controller} from 'cerebral'
import {Container} from 'cerebral/preact'
import App from './App'

const controller = Controller({
  state: {
    foo: 'bar'
  }
})

render((
  <Container controller={controller}>
    <App />
  </Container>
), document.querySelector('#app'))
```

```js
import {h, Component} from 'preact'
import {connect} from 'cerebral/preact'
import {state, signal} from 'cerebral/tags'

export default connect({
  foo: state`foo`,
  click: signal`clicked`
},
  class MyComponent extends Component {
    render ({foo, click}) {
      return <div onClick={() => click()}>{foo}</div>
    }
  }
)
```

## Angularjs (BETA)
[Website](https://angularjs.org/)

**NPM**

`npm install angular --save`

**YARN**

`yarn add angular`

```js
import angular from 'angular'
import {addModule} from 'cerebral/angularjs'

addModule(angular)

angular.module('app', ['cerebral'])
  .config(function (cerebralProvider) {
    cerebralProvider.configure({
      state: {
        foo: 'bar'
      },

      // Special controller property to expose core
      // angular services to your signals
      services: ['$http', '$timeout']
    })
  })
```

```js
import angular from 'angular'
import {connect} from 'cerebral/angularjs'
import {state, signal} from 'cerebral/tags'

angular.component('myComponent', {
  template: '<div ng-click="click()">{{foo}}</div>',
  controller: connect({
    foo: state`foo`,
    click: signal`clicked`
  }, function MyController () {
    // Optionally add custom behaviour to controller
  })
})
```

## Vue (BETA)
[Website](https://vuejs.org/)

**NPM**

`npm install vue --save`

**YARN**

`yarn add vue`

```js
import Vue from 'vue/dist/vue'
import {Controller} from 'cerebral'
import {Container, connect} from 'cerebral/vue'

const controller = Controller({
  state: {
    foo: 'bar'
  }
})

 var app = new Vue({
  el: '#app',
  components: {
    container: Container(controller),
    'my-component': MyComponent
  }
})
```

**Note!** The HTML of the root element must use the *container*:

```html
<div id="app">
  <container>
    <my-component></my-component>
  </container>
</div>
```

```js
import {connect} from 'cerebral/vue'
import {state, signal} from 'cerebral/tags'

export default connect({
  foo: state`foo`,
  click: signal`clicked`
}, {
  template: '<div v-on:click="click()">{{foo}}</div>'
})
```

## Composing dependencies
You can compose your dependencies with other tags. Like collect state based on a property passed to the component. Or maybe grab state based on some other state.
```js
import React from 'react'
import {connect} from 'cerebral/react'
import {state, props} from 'cerebral/tags'

export default connect({
  isLoading: state`${props`module`}.isLoading`
},
  function App(props) {
    props.isLoading
  }
)
```

## Optimize rendering
Due to Cerebrals "render on path change" it is possible to optimize component rendering.

```js
import React from 'react'
import {connect} from 'cerebral/react'
import {state} from 'cerebral/tags'

export default connect({
  list: state`app.array.*`,
  map: state`app.map.*`,
},
  function App (props) {
    props.list // [0, 1, 2, 3]
    props.map // ['foo', 'bar']
  }
)
```

This component will only render when any keys are added or removed, meaning that nested change to a child does not cause a new render.