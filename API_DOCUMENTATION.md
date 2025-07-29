# Vue.js API Documentation

## Table of Contents

1. [Overview](#overview)
2. [Global API](#global-api)
   - [Vue.extend](#vueextend)
   - [Vue.nextTick](#vuenexttick)
   - [Vue.set](#vueset)
   - [Vue.delete](#vuedelete)
   - [Vue.directive](#vuedirective)
   - [Vue.filter](#vuefilter)
   - [Vue.component](#vuecomponent)
   - [Vue.use](#vueuse)
   - [Vue.mixin](#vuemixin)
   - [Vue.compile](#vuecompile)
   - [Vue.version](#vueversion)
3. [Options API](#options-api)
   - [Data](#data)
   - [DOM](#dom)
   - [Lifecycle Hooks](#lifecycle-hooks)
   - [Assets](#assets)
   - [Composition](#composition)
   - [Misc](#misc)
4. [Instance Properties](#instance-properties)
5. [Instance Methods](#instance-methods)
   - [Data](#instance-data)
   - [Events](#instance-events)
   - [Lifecycle](#instance-lifecycle)
6. [Directives](#directives)
   - [v-text](#v-text)
   - [v-html](#v-html)
   - [v-show](#v-show)
   - [v-if](#v-if)
   - [v-else](#v-else)
   - [v-else-if](#v-else-if)
   - [v-for](#v-for)
   - [v-on](#v-on)
   - [v-bind](#v-bind)
   - [v-model](#v-model)
   - [v-slot](#v-slot)
   - [v-pre](#v-pre)
   - [v-cloak](#v-cloak)
   - [v-once](#v-once)
7. [Special Attributes](#special-attributes)
   - [key](#key)
   - [ref](#ref)
   - [is](#is)
   - [slot](#slot-deprecated)
   - [slot-scope](#slot-scope-deprecated)
8. [Built-in Components](#built-in-components)
   - [component](#component)
   - [transition](#transition)
   - [transition-group](#transition-group)
   - [keep-alive](#keep-alive)
   - [slot](#slot-component)
9. [VNode Interface](#vnode-interface)
10. [Server-Side Rendering](#server-side-rendering)

## Overview

Vue.js is a progressive JavaScript framework for building user interfaces. The core library focuses on the view layer only and is easy to integrate with other libraries or existing projects. Vue is also perfectly capable of powering sophisticated Single-Page Applications when used in combination with modern tooling and supporting libraries.

### Installation

```bash
# NPM
npm install vue

# Yarn
yarn add vue

# CDN
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
```

### Basic Usage

```javascript
// Creating a Vue instance
var vm = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})

// HTML
<div id="app">
  {{ message }}
</div>
```

## Global API

The Vue Global API provides methods that are available on the Vue constructor itself.

### Vue.extend

- **Arguments:**
  - `{Object} options`

- **Usage:**

  Create a "subclass" of the base Vue constructor. The argument should be an object containing component options.

  The special case to note here is the `data` option - it must be a function when used with `Vue.extend()`.

- **Example:**

  ```javascript
  // create a constructor
  var Profile = Vue.extend({
    template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
    data: function () {
      return {
        firstName: 'Walter',
        lastName: 'White',
        alias: 'Heisenberg'
      }
    }
  })
  
  // create an instance of Profile and mount it on an element
  new Profile().$mount('#mount-point')
  ```

  Will result in:

  ```html
  <p>Walter White aka Heisenberg</p>
  ```

### Vue.nextTick

- **Arguments:**
  - `{Function} [callback]`
  - `{Object} [context]`

- **Usage:**

  Defer the callback to be executed after the next DOM update cycle. Use it immediately after you've changed some data to wait for the DOM update.

- **Example:**

  ```javascript
  // modify data
  vm.msg = 'Hello'
  
  // DOM not updated yet
  Vue.nextTick(function () {
    // DOM updated
  })
  
  // usage as a promise (2.1.0+, see note below)
  Vue.nextTick()
    .then(function () {
      // DOM updated
    })
  ```

  > New in 2.1.0+: returns a Promise if no callback is provided and Promise is supported in the execution environment.

### Vue.set

- **Arguments:**
  - `{Object | Array} target`
  - `{string | number} propertyName/index`
  - `{any} value`

- **Returns:** the set value.

- **Usage:**

  Adds a property to a reactive object, ensuring the new property is also reactive, so it triggers view updates. This must be used to add new properties to reactive objects, as Vue cannot detect normal property additions (e.g. `this.myObject.newProperty = 'hi'`).

- **Example:**

  ```javascript
  // For objects
  Vue.set(vm.someObject, 'b', 2)
  // same as: vm.$set(vm.someObject, 'b', 2)
  
  // For arrays
  Vue.set(vm.items, indexOfItem, newValue)
  // same as: vm.$set(vm.items, indexOfItem, newValue)
  ```

### Vue.delete

- **Arguments:**
  - `{Object | Array} target`
  - `{string | number} propertyName/index`

- **Usage:**

  Delete a property on an object. If the object is reactive, ensure the deletion triggers view updates. This is primarily used to get around the limitation that Vue cannot detect property deletions, but you should rarely need to use it.

- **Example:**

  ```javascript
  // For objects
  Vue.delete(vm.someObject, 'a')
  // same as: vm.$delete(vm.someObject, 'a')
  
  // For arrays
  Vue.delete(vm.items, indexOfItem)
  // same as: vm.$delete(vm.items, indexOfItem)
  ```

### Vue.directive

- **Arguments:**
  - `{string} id`
  - `{Function | Object} [definition]`

- **Usage:**

  Register or retrieve a global directive.

- **Example:**

  ```javascript
  // register
  Vue.directive('my-directive', {
    bind: function (el, binding, vnode) {
      // some logic...
    },
    inserted: function (el, binding, vnode) {
      // ...
    },
    update: function (el, binding, vnode, oldVnode) {
      // ...
    },
    componentUpdated: function (el, binding, vnode, oldVnode) {
      // ...
    },
    unbind: function (el, binding, vnode) {
      // ...
    }
  })
  
  // register (function directive)
  Vue.directive('my-directive', function (el, binding) {
    // this will be called as `bind` and `update`
  })
  
  // getter, return the directive definition if registered
  var myDirective = Vue.directive('my-directive')
  ```

### Vue.filter

- **Arguments:**
  - `{string} id`
  - `{Function} [definition]`

- **Usage:**

  Register or retrieve a global filter.

- **Example:**

  ```javascript
  // register
  Vue.filter('capitalize', function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  })
  
  // getter, return the filter if registered
  var capitalizeFilter = Vue.filter('capitalize')
  
  // usage in templates
  <!-- in mustaches -->
  {{ message | capitalize }}
  
  <!-- in v-bind -->
  <div v-bind:id="rawId | formatId"></div>
  ```

### Vue.component

- **Arguments:**
  - `{string} id`
  - `{Function | Object} [definition]`

- **Usage:**

  Register or retrieve a global component. Registration also automatically sets the component's `name` with the given `id`.

- **Example:**

  ```javascript
  // register an extended constructor
  Vue.component('my-component', Vue.extend({ /* ... */ }))
  
  // register an options object (automatically calls Vue.extend)
  Vue.component('my-component', { /* ... */ })
  
  // retrieve a registered component (always return constructor)
  var MyComponent = Vue.component('my-component')
  
  // example usage
  Vue.component('button-counter', {
    data: function () {
      return {
        count: 0
      }
    },
    template: '<button @click="count++">You clicked me {{ count }} times.</button>'
  })
  ```

### Vue.use

- **Arguments:**
  - `{Object | Function} plugin`
  - `{Object} [options]`

- **Usage:**

  Install a Vue.js plugin. If the plugin is an Object, it must expose an `install` method. If it is a function itself, it will be treated as the install method. The install method will be called with Vue as the argument.

  This method has to be called before calling `new Vue()`

  When this method is called on the same plugin multiple times, the plugin will be installed only once.

- **Example:**

  ```javascript
  // plugin definition
  const MyPlugin = {
    install(Vue, options) {
      // 1. add global method or property
      Vue.myGlobalMethod = function () {
        // something logic ...
      }
  
      // 2. add a global asset
      Vue.directive('my-directive', {
        bind (el, binding, vnode, oldVnode) {
          // something logic ...
        }
      })
  
      // 3. inject some component options
      Vue.mixin({
        created: function () {
          // something logic ...
        }
      })
  
      // 4. add an instance method
      Vue.prototype.$myMethod = function (methodOptions) {
        // something logic ...
      }
    }
  }
  
  // usage
  Vue.use(MyPlugin, { someOption: true })
  ```

### Vue.mixin

- **Arguments:**
  - `{Object} mixin`

- **Usage:**

  Apply a mixin globally, which affects every Vue instance created afterwards. This can be used by plugin authors to inject custom behavior into components. **Not recommended in application code**.

- **Example:**

  ```javascript
  // inject a handler for `myOption` custom option
  Vue.mixin({
    created: function () {
      var myOption = this.$options.myOption
      if (myOption) {
        console.log(myOption)
      }
    }
  })
  
  new Vue({
    myOption: 'hello!'
  })
  // => "hello!"
  ```

### Vue.compile

- **Arguments:**
  - `{string} template`

- **Usage:**

  Compiles a template string into a render function. **Only available in the full build.**

- **Example:**

  ```javascript
  var res = Vue.compile('<div><span>{{ msg }}</span></div>')
  
  new Vue({
    data: {
      msg: 'hello'
    },
    render: res.render,
    staticRenderFns: res.staticRenderFns
  })
  ```

### Vue.version

- **Details:** Provides the installed version of Vue as a string. This is especially useful for community plugins and components, where you might use different strategies for different versions.

- **Example:**

  ```javascript
  var version = Number(Vue.version.split('.')[0])
  
  if (version === 2) {
    // Vue v2.x.x
  } else if (version === 1) {
    // Vue v1.x.x
  } else {
    // Unsupported versions of Vue
  }
  ```

### Vue.config

- **Type:** `Object`

- **Details:**

  Vue.config is an object containing Vue's global configurations. You can modify its properties listed below before bootstrapping your application:

  - **silent**
    - Type: `boolean`
    - Default: `false`
    - Usage:
      ```javascript
      Vue.config.silent = true
      // suppress all Vue logs and warnings
      ```

  - **optionMergeStrategies**
    - Type: `{ [key: string]: Function }`
    - Default: `{}`
    - Usage:
      ```javascript
      Vue.config.optionMergeStrategies._my_option = function (parent, child, vm) {
        return child + 1
      }
      ```

  - **devtools**
    - Type: `boolean`
    - Default: `true` (in development builds)
    - Usage:
      ```javascript
      // make sure to set this synchronously immediately after loading Vue
      Vue.config.devtools = true
      ```

  - **errorHandler**
    - Type: `Function`
    - Default: `undefined`
    - Usage:
      ```javascript
      Vue.config.errorHandler = function (err, vm, info) {
        // handle error
        // `info` is a Vue-specific error info, e.g. which lifecycle hook
        // the error was found in. Only available in 2.2.0+
      }
      ```

  - **warnHandler**
    - Type: `Function`
    - Default: `undefined`
    - Usage:
      ```javascript
      Vue.config.warnHandler = function (msg, vm, trace) {
        // `trace` is the component hierarchy trace
      }
      ```

  - **ignoredElements**
    - Type: `Array<string | RegExp>`
    - Default: `[]`
    - Usage:
      ```javascript
      Vue.config.ignoredElements = [
        'my-custom-web-component',
        'another-web-component',
        // Use a `RegExp` to ignore all elements that start with "ion-"
        /^ion-/
      ]
      ```

  - **keyCodes**
    - Type: `{ [key: string]: number | Array<number> }`
    - Default: `{}`
    - Usage:
      ```javascript
      Vue.config.keyCodes = {
        v: 86,
        f1: 112,
        // camelCase won`t work
        mediaPlayPause: 179,
        // instead you can use kebab-case with double quotation marks
        "media-play-pause": 179,
        up: [38, 87]
      }
      ```

  - **performance**
    - Type: `boolean`
    - Default: `false`
    - Usage:
      ```javascript
      Vue.config.performance = true
      // enable component init, compile, render and patch performance tracing in the browser devtool timeline
      ```

  - **productionTip**
    - Type: `boolean`
    - Default: `true`
    - Usage:
      ```javascript
      Vue.config.productionTip = false
      // prevent the production tip on Vue startup
      ```

## Options API

When creating a Vue instance or component, you pass in an options object. This section covers all available options.

### Data

#### data

- **Type:** `Object | Function`

- **Restriction:** Only accepts `Function` when used in a component definition.

- **Details:**

  The data object for the Vue instance. Vue will recursively convert its properties into getter/setters to make it "reactive". **The object must be plain**: native objects such as browser API objects and prototype properties are ignored. A rule of thumb is that data should just be data - it is not recommended to observe objects with their own stateful behavior.

  Once observed, you can no longer add reactive properties to the root data object. It is therefore recommended to declare all root-level reactive properties upfront, before creating the instance.

  After the instance is created, the original data object can be accessed as `vm.$data`. The Vue instance also proxies all the properties found on the data object, so `vm.a` will be equivalent to `vm.$data.a`.

- **Example:**

  ```javascript
  // direct instance creation
  var data = { a: 1 }
  var vm = new Vue({
    data: data
  })
  vm.a // => 1
  vm.$data === data // => true
  
  // must use function when in Vue.extend()
  var Component = Vue.extend({
    data: function () {
      return { a: 1 }
    }
  })
  
  // component definition
  Vue.component('my-component', {
    data: function () {
      return {
        count: 0
      }
    }
  })
  ```

#### props

- **Type:** `Array<string> | Object`

- **Details:**

  A list/hash of attributes that are exposed to accept data from the parent component. It has an Array-based simple syntax and an alternative Object-based syntax that allows advanced configurations such as type checking, custom validation and default values.

  With Object-based syntax, you can use the following options:
  - `type`: can be one of the following native constructors: `String`, `Number`, `Boolean`, `Array`, `Object`, `Date`, `Function`, `Symbol`, any custom constructor function or an array of those. Will check if a prop has a given type, and will throw a warning if it doesn't. [More information](https://vuejs.org/v2/guide/components-props.html#Prop-Types) on prop types.
  - `default`: `any`
    Specifies a default value for the prop. If the prop is not passed, this value will be used instead. Object or array defaults must be returned from a factory function.
  - `required`: `Boolean`
    Defines if the prop is required. In a non-production environment, a console warning will be thrown if this value is truthy and the prop is not passed.
  - `validator`: `Function`
    Custom validator function that takes the prop value as the sole argument. In a non-production environment, a console warning will be thrown if this function returns a falsy value (i.e. the validation fails).

- **Example:**

  ```javascript
  // simple syntax
  Vue.component('props-demo-simple', {
    props: ['size', 'myMessage']
  })
  
  // object syntax with validation
  Vue.component('props-demo-advanced', {
    props: {
      // type check
      height: Number,
      // type check plus other validations
      age: {
        type: Number,
        default: 0,
        required: true,
        validator: function (value) {
          return value >= 0
        }
      }
    }
  })
  ```

#### propsData

- **Type:** `{ [key: string]: any }`

- **Restriction:** only respected in instance creation via `new`.

- **Details:**

  Pass props to an instance during its creation. This is primarily intended to make unit testing easier.

- **Example:**

  ```javascript
  var Comp = Vue.extend({
    props: ['msg'],
    template: '<div>{{ msg }}</div>'
  })
  
  var vm = new Comp({
    propsData: {
      msg: 'hello'
    }
  })
  ```

#### computed

- **Type:** `{ [key: string]: Function | { get: Function, set: Function } }`

- **Details:**

  Computed properties to be mixed into the Vue instance. All getters and setters have their `this` context automatically bound to the Vue instance.

  Note that **you should not use an arrow function to define a computed property** (e.g. `aDouble: () => this.a * 2`). The reason is arrow functions bind the parent context, so `this` will not be the Vue instance as you expect and `this.a` will be undefined.

  Computed properties are cached, and only re-computed on reactive dependency changes. Note that if a certain dependency is out of the instance's scope (i.e. not reactive), the computed property will __not__ be updated.

- **Example:**

  ```javascript
  var vm = new Vue({
    data: { a: 1 },
    computed: {
      // get only
      aDouble: function () {
        return this.a * 2
      },
      // both get and set
      aPlus: {
        get: function () {
          return this.a + 1
        },
        set: function (v) {
          this.a = v - 1
        }
      }
    }
  })
  vm.aPlus   // => 2
  vm.aPlus = 3
  vm.a       // => 2
  vm.aDouble // => 4
  ```

#### methods

- **Type:** `{ [key: string]: Function }`

- **Details:**

  Methods to be mixed into the Vue instance. You can access these methods directly on the VM instance, or use them in directive expressions. All methods will have their `this` context automatically bound to the Vue instance.

  **Note that you should not use an arrow function to define a method** (e.g. `plus: () => this.a++`). The reason is the same as with computed properties.

- **Example:**

  ```javascript
  var vm = new Vue({
    data: { a: 1 },
    methods: {
      plus: function () {
        this.a++
      }
    }
  })
  vm.plus()
  vm.a // 2
  ```

#### watch

- **Type:** `{ [key: string]: string | Function | Object | Array }`

- **Details:**

  An object where keys are expressions to watch and values are the corresponding callbacks. The value can also be a string of a method name, or an Object that contains additional options. The Vue instance will call `$watch()` for each entry in the object at instantiation.

- **Example:**

  ```javascript
  var vm = new Vue({
    data: {
      a: 1,
      b: 2,
      c: 3,
      d: 4,
      e: {
        f: {
          g: 5
        }
      }
    },
    watch: {
      a: function (val, oldVal) {
        console.log('new: %s, old: %s', val, oldVal)
      },
      // string method name
      b: 'someMethod',
      // the callback will be called whenever any of the watched object properties change regardless of their nested depth
      c: {
        handler: function (val, oldVal) { /* ... */ },
        deep: true
      },
      // the callback will be called immediately after the start of the observation
      d: {
        handler: 'someMethod',
        immediate: true
      },
      // you can pass array of callbacks, they will be called one-by-one
      e: [
        'handle1',
        function handle2 (val, oldVal) { /* ... */ },
        {
          handler: function handle3 (val, oldVal) { /* ... */ },
          /* ... */
        }
      ],
      // watch vm.e.f's value: {g: 5}
      'e.f': function (val, oldVal) { /* ... */ }
    }
  })
  vm.a = 2 // => new: 2, old: 1
  ```

### DOM

#### el

- **Type:** `string | Element`

- **Restriction:** only respected in instance creation via `new`.

- **Details:**

  Provide the Vue instance an existing DOM element to mount on. It can be a CSS selector string or an actual HTMLElement.

  After the instance is mounted, the resolved element will be accessible as `vm.$el`.

  If this option is available at instantiation, the instance will immediately enter compilation; otherwise, the user will have to explicitly call `vm.$mount()` to manually start the compilation.

- **Example:**

  ```javascript
  // CSS selector
  new Vue({
    el: '#app'
  })
  
  // DOM element
  new Vue({
    el: document.getElementById('app')
  })
  ```

#### template

- **Type:** `string`

- **Details:**

  A string template to be used as the markup for the Vue instance. The template will **replace** the mounted element. Any existing markup inside the mounted element will be ignored, unless content distribution slots are present in the template.

  If the string starts with `#` it will be used as a querySelector and use the selected element's innerHTML as the template string. This allows the use of the common `<script type="x-template">` trick to include templates.

- **Example:**

  ```javascript
  // inline template
  new Vue({
    template: '<div>Hello {{ name }}</div>',
    data: {
      name: 'World'
    }
  })
  
  // template from script tag
  <script type="x-template" id="hello-world-template">
    <p>Hello hello hello</p>
  </script>
  
  new Vue({
    template: '#hello-world-template'
  })
  ```

#### render

- **Type:** `(createElement: () => VNode) => VNode`

- **Details:**

  An alternative to string templates allowing you to leverage the full programmatic power of JavaScript. The render function receives a `createElement` method as the first argument used to create `VNode`s.

  If the component is a functional component, the render function also receives an extra argument `context`, which provides access to contextual data since functional components are instance-less.

- **Example:**

  ```javascript
  Vue.component('anchored-heading', {
    render: function (createElement) {
      return createElement(
        'h' + this.level,   // tag name
        this.$slots.default // array of children
      )
    },
    props: {
      level: {
        type: Number,
        required: true
      }
    }
  })
  ```

#### renderError

- **Type:** `(createElement: () => VNode, error: Error) => VNode`

- **Details:**

  **Only works in development mode.**

  Provide an alternative render output when the default `render` function encounters an error. The error will be passed to `renderError` as the second argument. This is particularly useful when used together with hot-reload.

- **Example:**

  ```javascript
  new Vue({
    render (h) {
      throw new Error('oops')
    },
    renderError (h, err) {
      return h('pre', { style: { color: 'red' }}, err.stack)
    }
  }).$mount('#app')
  ```

### Lifecycle Hooks

All lifecycle hooks automatically have their `this` context bound to the instance, so that you can access data, computed properties, and methods. This means **you should not use an arrow function to define a lifecycle method** (e.g. `created: () => this.fetchTodos()`). The reason is arrow functions bind the parent context, so `this` will not be the Vue instance as you expect and `this.fetchTodos` will be undefined.

#### beforeCreate

- **Type:** `Function`

- **Details:**

  Called synchronously immediately after the instance has been initialized, before data observation and event/watcher setup.

#### created

- **Type:** `Function`

- **Details:**

  Called synchronously after the instance is created. At this stage, the instance has finished processing the options which means the following have been set up: data observation, computed properties, methods, watch/event callbacks. However, the mounting phase has not been started, and the `$el` property will not be available yet.

#### beforeMount

- **Type:** `Function`

- **Details:**

  Called right before the mounting begins: the `render` function is about to be called for the first time.

  **This hook is not called during server-side rendering.**

#### mounted

- **Type:** `Function`

- **Details:**

  Called after the instance has been mounted, where `el` is replaced by the newly created `vm.$el`. If the root instance is mounted to an in-document element, `vm.$el` will also be in-document when `mounted` is called.

  Note that `mounted` does **not** guarantee that all child components have also been mounted. If you want to wait until the entire view has been rendered, you can use `vm.$nextTick` inside of `mounted`:

  ```javascript
  mounted: function () {
    this.$nextTick(function () {
      // Code that will run only after the
      // entire view has been rendered
    })
  }
  ```

  **This hook is not called during server-side rendering.**

#### beforeUpdate

- **Type:** `Function`

- **Details:**

  Called when data changes, before the DOM is patched. This is a good place to access the existing DOM before an update, e.g. to remove manually added event listeners.

  **This hook is not called during server-side rendering, because only the initial render is performed server-side.**

#### updated

- **Type:** `Function`

- **Details:**

  Called after a data change causes the virtual DOM to be re-rendered and patched.

  The component's DOM will have been updated when this hook is called, so you can perform DOM-dependent operations here. However, in most cases you should avoid changing state inside the hook. To react to state changes, it's usually better to use a [computed property](#computed) or [watcher](#watch) instead.

  Note that `updated` does **not** guarantee that all child components have also been re-rendered. If you want to wait until the entire view has been re-rendered, you can use `vm.$nextTick` inside of `updated`:

  ```javascript
  updated: function () {
    this.$nextTick(function () {
      // Code that will run only after the
      // entire view has been re-rendered
    })
  }
  ```

  **This hook is not called during server-side rendering.**

#### activated

- **Type:** `Function`

- **Details:**

  Called when a kept-alive component is activated.

  **This hook is not called during server-side rendering.**

#### deactivated

- **Type:** `Function`

- **Details:**

  Called when a kept-alive component is deactivated.

  **This hook is not called during server-side rendering.**

#### beforeDestroy

- **Type:** `Function`

- **Details:**

  Called right before a Vue instance is destroyed. At this stage the instance is still fully functional.

  **This hook is not called during server-side rendering.**

#### destroyed

- **Type:** `Function`

- **Details:**

  Called after a Vue instance has been destroyed. When this hook is called, all directives of the Vue instance have been unbound, all event listeners have been removed, and all child Vue instances have also been destroyed.

  **This hook is not called during server-side rendering.**

#### errorCaptured

- **Type:** `(err: Error, vm: Component, info: string) => ?boolean`

- **Details:**

  Called when an error from any descendent component is captured. The hook receives three arguments: the error, the component instance that triggered the error, and a string containing information on where the error was captured. The hook can return `false` to stop the error from propagating further.

  You can modify component state in this hook. However, it is important to have conditionals in your template or render function that short circuits other content when an error has been captured; otherwise the component will be thrown into an infinite render loop.

  **Error Propagation Rules**

  - By default, all errors are still sent to the global `config.errorHandler` if it is defined, so that these errors can still be reported to an analytics service in a single place.

  - If multiple `errorCaptured` hooks exist on a component's inheritance chain or parent chain, all of them will be invoked on the same error.

  - If the `errorCaptured` hook itself throws an error, both this error and the original captured error are sent to the global `config.errorHandler`.

  - An `errorCaptured` hook can return `false` to prevent the error from propagating further. This is essentially saying "this error has been handled and should be ignored." It will prevent any additional `errorCaptured` hooks or the global `config.errorHandler` from being invoked for this error.

### Assets

#### directives

- **Type:** `Object`

- **Details:**

  A hash of directives to be made available to the Vue instance.

- **Example:**

  ```javascript
  Vue.component('my-component', {
    directives: {
      // directive definition
      focus: {
        inserted: function (el) {
          el.focus()
        }
      }
    },
    template: '<input v-focus>'
  })
  ```

#### filters

- **Type:** `Object`

- **Details:**

  A hash of filters to be made available to the Vue instance.

- **Example:**

  ```javascript
  Vue.component('my-component', {
    filters: {
      capitalize: function (value) {
        if (!value) return ''
        value = value.toString()
        return value.charAt(0).toUpperCase() + value.slice(1)
      }
    },
    template: '<p>{{ message | capitalize }}</p>',
    data: function () {
      return {
        message: 'hello'
      }
    }
  })
  ```

#### components

- **Type:** `Object`

- **Details:**

  A hash of components to be made available to the Vue instance.

- **Example:**

  ```javascript
  var Child = {
    template: '<div>A child component!</div>'
  }
  
  new Vue({
    components: {
      // register locally
      'my-component': Child
    },
    template: '<my-component></my-component>'
  })
  ```

### Composition

#### parent

- **Type:** `Vue instance`

- **Details:**

  Specify the parent instance for the instance to be created. Establishes a parent-child relationship between the two. The parent will be accessible as `this.$parent` for the child, and the child will be pushed into the parent's `$children` array.

  > Use `$parent` and `$children` sparingly - they mostly serve as an escape-hatch. Prefer using props and events for parent-child communication.

#### mixins

- **Type:** `Array<Object>`

- **Details:**

  The `mixins` option accepts an array of mixin objects. These mixin objects can contain instance options like normal instance objects, and they will be merged against the eventual options using the same option merging logic in `Vue.extend()`. e.g. If your mixin contains a created hook and the component itself also has one, both functions will be called.

  Mixin hooks are called in the order they are provided, and called before the component's own hooks.

- **Example:**

  ```javascript
  var mixin = {
    created: function () { console.log(1) }
  }
  var vm = new Vue({
    created: function () { console.log(2) },
    mixins: [mixin]
  })
  // => 1
  // => 2
  ```

#### extends

- **Type:** `Object | Function`

- **Details:**

  Allows declaratively extending another component (could be either a plain options object or a constructor) without having to use `Vue.extend`. This is primarily intended to make it easier to extend between single file components.

  This is similar to `mixins`.

- **Example:**

  ```javascript
  var CompA = { ... }
  
  // extend CompA without having to call `Vue.extend` on either
  var CompB = {
    extends: CompA,
    ...
  }
  ```

#### provide / inject

- **Type:**
  - **provide:** `Object | () => Object`
  - **inject:** `Array<string> | { [key: string]: string | Symbol | Object }`

- **Details:**

  These pair of options are used together to allow an ancestor component to serve as a dependency injector for all its descendants, regardless of how deep the component hierarchy is, as long as they are in the same parent chain. If you are familiar with React, this is very similar to React's context feature.

  The `provide` option should be an object or a function that returns an object. This object contains the properties that are available for injection into its descendants. You can use ES2015 Symbols as keys in this object, but only in environments that natively support `Symbol` and `Reflect.ownKeys`.

  The `inject` option should be either:
  - an array of strings, or
  - an object where the keys are the local binding name and the value is either:
    - the key (string or Symbol) to search for in available injections, or
    - an object where:
      - the `from` property is the key (string or Symbol) to search for in available injections, and
      - the `default` property is used as fallback value

  > Note: the `provide` and `inject` bindings are NOT reactive. This is intentional. However, if you pass down an observed object, properties on that object do remain reactive.

- **Example:**

  ```javascript
  // parent component providing 'foo'
  var Provider = {
    provide: {
      foo: 'bar'
    },
    // ...
  }
  
  // child component injecting 'foo'
  var Child = {
    inject: ['foo'],
    created () {
      console.log(this.foo) // => "bar"
    }
    // ...
  }
  ```

  With ES2015 Symbols, function `provide` and object `inject`:

  ```javascript
  const s = Symbol()
  
  const Provider = {
    provide () {
      return {
        [s]: 'foo'
      }
    }
  }
  
  const Child = {
    inject: { s },
    // ...
  }
  ```

  Using an injected value as the default for a prop:

  ```javascript
  const Child = {
    inject: ['foo'],
    props: {
      bar: {
        default () {
          return this.foo
        }
      }
    }
  }
  ```

  Using an injected value as data entry:

  ```javascript
  const Child = {
    inject: ['foo'],
    data () {
      return {
        bar: this.foo
      }
    }
  }
  ```

  Injections can be optional with default value:

  ```javascript
  const Child = {
    inject: {
      foo: { default: 'foo' }
    }
  }
  ```

  If it needs to be injected from a property with a different name, use `from` to denote the source property:

  ```javascript
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: 'foo'
      }
    }
  }
  ```

  Similar to prop defaults, you need to use a factory function for non-primitive values:

  ```javascript
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: () => [1, 2, 3]
      }
    }
  }
  ```

### Misc

#### name

- **Type:** `string`

- **Restriction:** only respected when used as a component option.

- **Details:**

  Allow the component to recursively invoke itself in its template. Note that when a component is registered globally with `Vue.component()`, the global ID is automatically set as its name.

  Another benefit of specifying a `name` option is debugging. Named components result in more helpful warning messages. Also, when inspecting an app in the [vue-devtools](https://github.com/vuejs/vue-devtools), unnamed components will show up as `<AnonymousComponent>`, which isn't very informative. By providing the `name` option, you will get a much more informative component tree.

#### delimiters

- **Type:** `Array<string>`

- **Default:** `["{{", "}}"]`

- **Restrictions:** This option is only available in the full build, with in-browser compilation.

- **Details:**

  Change the plain text interpolation delimiters.

- **Example:**

  ```javascript
  new Vue({
    delimiters: ['${', '}']
  })
  
  // Delimiters changed to ES6 template string style
  ```

#### functional

- **Type:** `boolean`

- **Details:**

  Causes a component to be stateless (no `data`) and instanceless (no `this` context). They are only a `render` function that returns virtual nodes making them much cheaper to render.

#### model

- **Type:** `{ prop?: string, event?: string }`

- **Details:**

  Allows a custom component to customize the prop and event used when it's used with `v-model`. By default, `v-model` on a component uses `value` as the prop and `input` as the event, but some input types such as checkboxes and radio buttons may want to use the `value` prop for a different purpose. Using the `model` option can avoid the conflict in such cases.

- **Example:**

  ```javascript
  Vue.component('my-checkbox', {
    model: {
      prop: 'checked',
      event: 'change'
    },
    props: {
      // this allows using the `value` prop for a different purpose
      value: String,
      // use `checked` as the prop which take the place of `value`
      checked: {
        type: Boolean,
        default: false
      }
    },
    template: `
      <input
        type="checkbox"
        v-bind:checked="checked"
        v-on:change="$emit('change', $event.target.checked)"
      >
    `
  })
  ```

  ```html
  <my-checkbox v-model="foo" value="some value"></my-checkbox>
  ```

  The above will be equivalent to:

  ```html
  <my-checkbox
    :checked="foo"
    @change="val => { foo = val }"
    value="some value">
  </my-checkbox>
  ```

#### inheritAttrs

- **Type:** `boolean`

- **Default:** `true`

- **Details:**

  By default, parent scope attribute bindings that are not recognized as props will "fallthrough" and be applied to the root element of the child component as normal HTML attributes. When authoring a component that wraps a target element or another component, this may not always be the desired behavior. By setting `inheritAttrs` to `false`, this default behavior can be disabled. The attributes are available via the `$attrs` instance property (also new in 2.4) and can be explicitly bound to a non-root element using `v-bind`.

  Note: this option does **not** affect `class` and `style` bindings.

#### comments

- **Type:** `boolean`

- **Default:** `false`

- **Restrictions:** This option is only available in the full build, with in-browser compilation.

- **Details:**

  When set to `true`, will preserve and render HTML comments found in templates. The default behavior is discarding them.

## Instance Properties

### vm.$data

- **Type:** `Object`

- **Details:**

  The data object that the Vue instance is observing. The Vue instance proxies access to the properties on its data object.

- **See also:** [Options / Data - data](#data)

### vm.$props

- **Type:** `Object`

- **Details:**

  An object representing the current props a component has received. The Vue instance proxies access to the properties on its props object.

### vm.$el

- **Type:** `Element`

- **Read only**

- **Details:**

  The root DOM element that the Vue instance is managing.

### vm.$options

- **Type:** `Object`

- **Read only**

- **Details:**

  The instantiation options used for the current Vue instance. This is useful when you want to include custom properties in options:

  ```javascript
  new Vue({
    customOption: 'foo',
    created: function () {
      console.log(this.$options.customOption) // => 'foo'
    }
  })
  ```

### vm.$parent

- **Type:** `Vue instance`

- **Read only**

- **Details:**

  The parent instance, if the current instance has one.

### vm.$root

- **Type:** `Vue instance`

- **Read only**

- **Details:**

  The root Vue instance of the current component tree. If the current instance has no parents this value will be itself.

### vm.$children

- **Type:** `Array<Vue instance>`

- **Read only**

- **Details:**

  The direct child components of the current instance. **Note there's no order guarantee for `$children`, and it is not reactive.** If you find yourself trying to use `$children` for data binding, consider using an Array and `v-for` to generate child components, and use the Array as the source of truth.

### vm.$slots

- **Type:** `{ [name: string]: ?Array<VNode> }`

- **Read only**

- **Reactive:** No

- **Details:**

  Used to programmatically access content [distributed by slots](https://vuejs.org/v2/guide/components-slots.html). Each [named slot](https://vuejs.org/v2/guide/components-slots.html#Named-Slots) has its own corresponding property (e.g. the contents of `v-slot:foo` will be found at `vm.$slots.foo`). The `default` property contains either nodes not included in a named slot or contents of `v-slot:default`.

  **Note:** `v-slot:foo` is supported in v2.6+. For older versions, you can use the [deprecated syntax](https://vuejs.org/v2/guide/components-slots.html#Deprecated-Syntax).

  Accessing `vm.$slots` is most useful when writing a component with a [render function](https://vuejs.org/v2/guide/render-function.html).

- **Example:**

  ```html
  <blog-post>
    <template v-slot:header>
      <h1>About Me</h1>
    </template>

    <p>Here's some page content, which will be included in vm.$slots.default, because it's not inside a named slot.</p>

    <template v-slot:footer>
      <p>Copyright 2016 Evan You</p>
    </template>

    <p>If I have some content down here, it will also be included in vm.$slots.default.</p>
  </blog-post>
  ```

  ```javascript
  Vue.component('blog-post', {
    render: function (createElement) {
      var header = this.$slots.header
      var body   = this.$slots.default
      var footer = this.$slots.footer
      return createElement('div', [
        createElement('header', header),
        createElement('main', body),
        createElement('footer', footer)
      ])
    }
  })
  ```

### vm.$scopedSlots

- **Type:** `{ [name: string]: props => Array<VNode> | undefined }`

- **Read only**

- **Details:**

  Used to programmatically access [scoped slots](https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots). For each slot, including the `default` one, the object contains a corresponding function that returns VNodes.

  Accessing `vm.$scopedSlots` is most useful when writing a component with a [render function](https://vuejs.org/v2/guide/render-function.html).

  **Note:** since 2.6.0+, there are two notable changes to this property:

  1. Scoped slot functions are now guaranteed to return an array of VNodes, unless the return value is invalid, in which case the function will return `undefined`.

  2. All `$slots` are now also exposed on `$scopedSlots` as functions. If you work with render functions, it is now recommended to always access slots via `$scopedSlots`, whether they currently use a scope or not. This will not only make future refactors to add a scope simpler, but also ease your eventual migration to Vue 3, where all slots will be functions.

### vm.$refs

- **Type:** `Object`

- **Read only**

- **Details:**

  An object of DOM elements and component instances, registered with [`ref` attributes](#ref).

- **Example:**

  ```javascript
  <div id="app">
    <input ref="input">
    <child-component ref="child"></child-component>
  </div>

  new Vue({
    el: '#app',
    mounted: function () {
      // Focus the input
      this.$refs.input.focus()
      // Access child component instance
      console.log(this.$refs.child.message)
    }
  })
  ```

### vm.$isServer

- **Type:** `boolean`

- **Read only**

- **Details:**

  Whether the current Vue instance is running on the server.

### vm.$attrs

- **Type:** `{ [key: string]: string }`

- **Read only**

- **Details:**

  Contains parent-scope attribute bindings (except for `class` and `style`) that are not recognized (and extracted) as props. When a component doesn't have any declared props, this essentially contains all parent-scope bindings (except for `class` and `style`), and can be passed down to an inner component via `v-bind="$attrs"` - useful when creating higher-order components.

### vm.$listeners

- **Type:** `{ [key: string]: Function | Array<Function> }`

- **Read only**

- **Details:**

  Contains parent-scope `v-on` event listeners (without `.native` modifiers). This can be passed down to an inner component via `v-on="$listeners"` - useful when creating transparent wrapper components.

## Instance Methods

### Data

#### vm.$watch

- **Arguments:**
  - `{string | Function} expOrFn`
  - `{Function | Object} callback`
  - `{Object} [options]`
    - `{boolean} deep`
    - `{boolean} immediate`

- **Returns:** `{Function} unwatch`

- **Usage:**

  Watch an expression or a computed function on the Vue instance for changes. The callback gets called with the new value and the old value. The expression only accepts dot-delimited paths. For more complex expressions, use a function instead.

- **Example:**

  ```javascript
  // keypath
  vm.$watch('a.b.c', function (newVal, oldVal) {
    // do something
  })

  // function
  vm.$watch(
    function () {
      // every time the expression `this.a + this.b` yields a different result,
      // the handler will be called. It's as if we were watching a computed
      // property without defining the computed property itself
      return this.a + this.b
    },
    function (newVal, oldVal) {
      // do something
    }
  )

  // with options
  var unwatch = vm.$watch('a', cb, {
    deep: true,
    immediate: true
  })

  // later, teardown the watcher
  unwatch()
  ```

  - **Option: deep**

    To also detect nested value changes inside Objects, you need to pass in `deep: true` in the options argument. Note that you don't need to do so to listen for Array mutations.

    ```javascript
    vm.$watch('someObject', callback, {
      deep: true
    })
    vm.someObject.nestedValue = 123
    // callback is fired
    ```

  - **Option: immediate**

    Passing in `immediate: true` in the option will trigger the callback immediately with the current value of the expression:

    ```javascript
    vm.$watch('a', callback, {
      immediate: true
    })
    // `callback` is fired immediately with current value of `a`
    ```

#### vm.$set

- **Arguments:**
  - `{Object | Array} target`
  - `{string | number} propertyName/index`
  - `{any} value`

- **Returns:** the set value.

- **Usage:**

  This is the **alias** of the global `Vue.set`.

- **See also:** [Vue.set](#vueset)

#### vm.$delete

- **Arguments:**
  - `{Object | Array} target`
  - `{string | number} propertyName/index`

- **Usage:**

  This is the **alias** of the global `Vue.delete`.

- **See also:** [Vue.delete](#vuedelete)

### Events

#### vm.$on

- **Arguments:**
  - `{string | Array<string>} event` (array only supported in 2.2.0+)
  - `{Function} callback`

- **Usage:**

  Listen for a custom event on the current vm. Events can be triggered by `vm.$emit`. The callback will receive all the additional arguments passed into these event-triggering methods.

- **Example:**

  ```javascript
  vm.$on('test', function (msg) {
    console.log(msg)
  })
  vm.$emit('test', 'hi')
  // => "hi"
  ```

#### vm.$once

- **Arguments:**
  - `{string} event`
  - `{Function} callback`

- **Usage:**

  Listen for a custom event, but only once. The listener will be removed once it triggers for the first time.

- **Example:**

  ```javascript
  vm.$once('test', function (msg) {
    console.log(msg)
  })
  vm.$emit('test', 'hi')
  // => "hi"
  vm.$emit('test', 'hi')
  // nothing happens
  ```

#### vm.$off

- **Arguments:**
  - `{string | Array<string>} event` (array only supported in 2.2.2+)
  - `{Function} [callback]`

- **Usage:**

  Remove custom event listener(s).

  - If no arguments are provided, remove all event listeners;

  - If only the event is provided, remove all listeners for that event;

  - If both event and callback are given, remove the listener for that specific callback only.

- **Example:**

  ```javascript
  // remove all listeners
  vm.$off()

  // remove all listeners for 'test'
  vm.$off('test')

  // remove the specific listener
  vm.$off('test', callback)
  ```

#### vm.$emit

- **Arguments:**
  - `{string} eventName`
  - `[...args]`

- **Usage:**

  Trigger an event on the current instance. Any additional arguments will be passed into the listener's callback function.

- **Examples:**

  Using `$emit` with only an event name:

  ```javascript
  Vue.component('welcome-button', {
    template: `
      <button v-on:click="$emit('welcome')">
        Click me to be welcomed
      </button>
    `
  })
  ```

  ```html
  <div id="emit-example-simple">
    <welcome-button v-on:welcome="sayHi"></welcome-button>
  </div>
  ```

  ```javascript
  new Vue({
    el: '#emit-example-simple',
    methods: {
      sayHi: function () {
        alert('Hi!')
      }
    }
  })
  ```

  Using `$emit` with additional arguments:

  ```javascript
  Vue.component('magic-eight-ball', {
    data: function () {
      return {
        possibleAdvice: ['Yes', 'No', 'Maybe']
      }
    },
    methods: {
      giveAdvice: function () {
        var randomAdviceIndex = Math.floor(Math.random() * this.possibleAdvice.length)
        this.$emit('give-advice', this.possibleAdvice[randomAdviceIndex])
      }
    },
    template: `
      <button v-on:click="giveAdvice">
        Click me for advice
      </button>
    `
  })
  ```

  ```html
  <div id="emit-example-argument">
    <magic-eight-ball v-on:give-advice="showAdvice"></magic-eight-ball>
  </div>
  ```

  ```javascript
  new Vue({
    el: '#emit-example-argument',
    methods: {
      showAdvice: function (advice) {
        alert(advice)
      }
    }
  })
  ```

### Lifecycle

#### vm.$mount

- **Arguments:**
  - `{Element | string} [elementOrSelector]`
  - `{boolean} [hydrating]`

- **Returns:** `vm` - the instance itself

- **Usage:**

  If a Vue instance didn't receive the `el` option at instantiation, it will be in "unmounted" state, without an associated DOM element. `vm.$mount()` can be used to manually start the mounting of an unmounted Vue instance.

  If `elementOrSelector` argument is not provided, the template will be rendered as an off-document element, and you will have to use native DOM API to insert it into the document yourself.

  The method returns the instance itself so you can chain other instance methods after it.

- **Example:**

  ```javascript
  var MyComponent = Vue.extend({
    template: '<div>Hello!</div>'
  })

  // create and mount to #app (will replace #app)
  new MyComponent().$mount('#app')

  // the above is the same as:
  new MyComponent({ el: '#app' })

  // or, render off-document and append afterwards:
  var component = new MyComponent().$mount()
  document.getElementById('app').appendChild(component.$el)
  ```

#### vm.$forceUpdate

- **Usage:**

  Force the Vue instance to re-render. Note it does not affect all child components, only the instance itself and child components with inserted slot content.

#### vm.$nextTick

- **Arguments:**
  - `{Function} [callback]`

- **Usage:**

  Defer the callback to be executed after the next DOM update cycle. Use it immediately after you've changed some data to wait for the DOM update. This is the same as the global `Vue.nextTick`, except that the callback's `this` context is automatically bound to the instance calling this method.

- **Example:**

  ```javascript
  new Vue({
    // ...
    methods: {
      // ...
      example: function () {
        // modify data
        this.message = 'changed'
        // DOM is not updated yet
        this.$nextTick(function () {
          // DOM is now updated
          // `this` is bound to the current instance
          this.doSomethingElse()
        })
      }
    }
  })
  ```

  > New in 2.1.0+: returns a Promise if no callback is provided and Promise is supported in the execution environment. Please note that Vue does not come with a Promise polyfill, so if you target browsers that don't support Promises natively (looking at you, IE), you will have to provide a polyfill yourself.

#### vm.$destroy

- **Usage:**

  Completely destroy a vm. Clean up its connections with other existing vms, unbind all its directives, turn off all event listeners.

  Triggers the `beforeDestroy` and `destroyed` hooks.

  > Note: In normal use cases you shouldn't have to call this method yourself. Prefer controlling the lifecycle of child components in a data-driven fashion using `v-if` and `v-for`.

## Directives

Directives are special attributes with the `v-` prefix. Directive attribute values are expected to be **a single JavaScript expression** (with the exception of `v-for`, which will be discussed later). A directive's job is to reactively apply side effects to the DOM when the value of its expression changes.

### v-text

- **Expects:** `string`

- **Details:**

  Updates the element's `textContent`. If you need to update the part of `textContent`, you should use `{{ Mustache }}` interpolations.

- **Example:**

  ```html
  <span v-text="msg"></span>
  <!-- same as -->
  <span>{{msg}}</span>
  ```

### v-html

- **Expects:** `string`

- **Details:**

  Updates the element's `innerHTML`. **Note that the contents are inserted as plain HTML - they will not be compiled as Vue templates**. If you find yourself trying to compose templates using `v-html`, try to rethink the solution by using components instead.

  > Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to [XSS attacks](https://en.wikipedia.org/wiki/Cross-site_scripting). Only use `v-html` on trusted content and **never** on user-provided content.

  > In [single-file components](https://vuejs.org/v2/guide/single-file-components.html), `scoped` styles will not apply to content inside `v-html`, because that HTML is not processed by Vue's template compiler. If you want to target `v-html` content with scoped CSS, you can instead use [CSS modules](https://vue-loader.vuejs.org/en/features/css-modules.html) or an additional, global `<style>` element with a manual scoping strategy such as BEM.

- **Example:**

  ```html
  <div v-html="html"></div>
  ```

### v-show

- **Expects:** `any`

- **Usage:**

  Toggles the element's `display` CSS property based on the truthy-ness of the expression value.

  This directive triggers transitions when its condition changes.

- **Example:**

  ```html
  <h1 v-show="ok">Hello!</h1>
  ```

### v-if

- **Expects:** `any`

- **Usage:**

  Conditionally render the element based on the truthy-ness of the expression value. The element and its contained directives / components are destroyed and re-constructed during toggles. If the element is a `<template>` element, its content will be extracted as the conditional block.

  This directive triggers transitions when its condition changes.

  > When used together with v-for, v-for has a higher priority than v-if. See the [list rendering guide](https://vuejs.org/v2/guide/list.html#v-for-with-v-if) for details.

- **Example:**

  ```html
  <h1 v-if="awesome">Vue is awesome!</h1>
  ```

### v-else

- **Does not expect expression**

- **Restriction:** previous sibling element must have `v-if` or `v-else-if`.

- **Usage:**

  Denote the "else block" for `v-if` or a `v-if`/`v-else-if` chain.

- **Example:**

  ```html
  <div v-if="Math.random() > 0.5">
    Now you see me
  </div>
  <div v-else>
    Now you don't
  </div>
  ```

### v-else-if

- **Expects:** `any`

- **Restriction:** previous sibling element must have `v-if` or `v-else-if`.

- **Usage:**

  Denote the "else if block" for `v-if`. Can be chained.

- **Example:**

  ```html
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else-if="type === 'C'">
    C
  </div>
  <div v-else>
    Not A/B/C
  </div>
  ```

### v-for

- **Expects:** `Array | Object | number | string | Iterable (since 2.6)`

- **Usage:**

  Render the element or template block multiple times based on the source data. The directive's value must use the special syntax `alias in expression` to provide an alias for the current element being iterated on:

  ```html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  Alternatively, you can also specify an alias for the index (or the key if used on an Object):

  ```html
  <div v-for="(item, index) in items"></div>
  <div v-for="(val, key) in object"></div>
  <div v-for="(val, name, index) in object"></div>
  ```

  The default behavior of `v-for` will try to patch the elements in-place without moving them. To force it to reorder elements, you should provide an ordering hint with the `key` special attribute:

  ```html
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```

  > In 2.6+, `v-for` can also work on values that implement the [Iterable Protocol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol), including native `Map` and `Set`. However, it should be noted that Vue 2.x currently does not support reactivity on `Map` and `Set` values, so it cannot automatically detect changes.

  > When used together with v-if, v-for has a higher priority than v-if. See the [list rendering guide](https://vuejs.org/v2/guide/list.html#v-for-with-v-if) for details.

  The detailed usage for `v-for` is explained in the guide section linked below.

- **Example:**

  ```html
  <!-- Array -->
  <li v-for="item in items" :key="item.id">
    {{ item.message }}
  </li>

  <!-- Array with index -->
  <li v-for="(item, index) in items" :key="index">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>

  <!-- Object -->
  <li v-for="value in object" :key="value">
    {{ value }}
  </li>

  <!-- Object with key -->
  <div v-for="(value, key) in object" :key="key">
    {{ key }}: {{ value }}
  </div>

  <!-- Object with key and index -->
  <div v-for="(value, name, index) in object" :key="name">
    {{ index }}. {{ name }}: {{ value }}
  </div>

  <!-- Range -->
  <span v-for="n in 10" :key="n">{{ n }} </span>

  <!-- Component -->
  <my-component
    v-for="item in items"
    :key="item.id"
    :item="item"
  ></my-component>

  <!-- Template -->
  <template v-for="item in items" :key="item.id">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
  ```

- **See also:** [List Rendering](https://vuejs.org/v2/guide/list.html)

### v-on

- **Shorthand:** `@`

- **Expects:** `Function | Inline Statement | Object`

- **Argument:** `event`

- **Modifiers:**
  - `.stop` - call `event.stopPropagation()`.
  - `.prevent` - call `event.preventDefault()`.
  - `.capture` - add event listener in capture mode.
  - `.self` - only trigger handler if event was dispatched from this element.
  - `.{keyCode | keyAlias}` - only trigger handler on certain keys.
  - `.native` - listen for a native event on the root element of component.
  - `.once` - trigger handler at most once.
  - `.left` - (2.2.0+) only trigger handler for left button mouse events.
  - `.right` - (2.2.0+) only trigger handler for right button mouse events.
  - `.middle` - (2.2.0+) only trigger handler for middle button mouse events.
  - `.passive` - (2.3.0+) attaches a DOM event with `{ passive: true }`.

- **Usage:**

  Attaches an event listener to the element. The event type is denoted by the argument. The expression can be a method name, an inline statement, or omitted if there are modifiers present.

  When used on a normal element, it listens to **native DOM events** only. When used on a custom element component, it listens to **custom events** emitted on that child component.

  When listening to native DOM events, the method receives the native event as the only argument. If using inline statement, the statement has access to the special `$event` property: `v-on:click="handle('ok', $event)"`.

  Starting in 2.4.0+, `v-on` also supports binding to an object of event/listener pairs without an argument. Note when using the object syntax, it does not support any modifiers.

- **Example:**

  ```html
  <!-- method handler -->
  <button v-on:click="doThis"></button>

  <!-- dynamic event (2.6.0+) -->
  <button v-on:[event]="doThis"></button>

  <!-- inline statement -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- shorthand -->
  <button @click="doThis"></button>

  <!-- shorthand dynamic event (2.6.0+) -->
  <button @[event]="doThis"></button>

  <!-- stop propagation -->
  <button @click.stop="doThis"></button>

  <!-- prevent default -->
  <button @click.prevent="doThis"></button>

  <!-- prevent default without expression -->
  <form @submit.prevent></form>

  <!-- chain modifiers -->
  <button @click.stop.prevent="doThis"></button>

  <!-- key modifier using keyAlias -->
  <input @keyup.enter="onEnter">

  <!-- key modifier using keyCode -->
  <input @keyup.13="onEnter">

  <!-- the click event will be triggered at most once -->
  <button v-on:click.once="doThis"></button>

  <!-- object syntax (2.4.0+) -->
  <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
  ```

  Listening to custom events on a child component (the handler is called when "my-event" is emitted on the child):

  ```html
  <my-component @my-event="handleThis"></my-component>

  <!-- inline statement -->
  <my-component @my-event="handleThis(123, $event)"></my-component>

  <!-- native event on component -->
  <my-component @click.native="onClick"></my-component>
  ```

- **See also:**
  - [Event Handling](https://vuejs.org/v2/guide/events.html)
  - [Components - Custom Events](https://vuejs.org/v2/guide/components-custom-events.html)

### v-bind

- **Shorthand:** `:`

- **Expects:** `any (with argument) | Object (without argument)`

- **Argument:** `attrOrProp (optional)`

- **Modifiers:**
  - `.prop` - Bind as a DOM property instead of an attribute ([what's the difference?](https://stackoverflow.com/questions/6003819/properties-and-attributes-in-html#answer-6004028)). If the tag is a component then `.prop` will set the property on the component's `$el`.
  - `.camel` - (2.1.0+) transform the kebab-case attribute name into camelCase.
  - `.sync` - (2.3.0+) a syntax sugar that expands into a `v-on` handler for updating the bound value.

- **Usage:**

  Dynamically bind one or more attributes, or a component prop to an expression.

  When used to bind the `class` or `style` attribute, it supports additional value types such as Array or Objects. See linked guide section below for more details.

  When used for prop binding, the prop must be properly declared in the child component.

  When used without an argument, can be used to bind an object containing attribute name-value pairs. Note in this mode `class` and `style` does not support Array or Objects.

- **Example:**

  ```html
  <!-- bind an attribute -->
  <img v-bind:src="imageSrc">

  <!-- dynamic attribute name (2.6.0+) -->
  <button v-bind:[key]="value"></button>

  <!-- shorthand -->
  <img :src="imageSrc">

  <!-- shorthand dynamic attribute name (2.6.0+) -->
  <button :[key]="value"></button>

  <!-- with inline string concatenation -->
  <img :src="'/path/to/images/' + fileName">

  <!-- class binding -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]">

  <!-- style binding -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- binding an object of attributes -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!-- DOM attribute binding with prop modifier -->
  <div v-bind:text-content.prop="text"></div>

  <!-- prop binding. "prop" must be declared in my-component. -->
  <my-component :prop="someThing"></my-component>

  <!-- pass down parent props in common with a child component -->
  <child-component v-bind="$props"></child-component>

  <!-- XLink -->
  <svg><a :xlink:special="foo"></a></svg>
  ```

  The `.camel` modifier allows camelizing a `v-bind` attribute name when using in-DOM templates, e.g. the SVG `viewBox` attribute:

  ```html
  <svg :view-box.camel="viewBox"></svg>
  ```

  `.camel` is not needed if you are using string templates, or compiling with `vue-loader`/`vueify`.

- **See also:**
  - [Class and Style Bindings](https://vuejs.org/v2/guide/class-and-style.html)
  - [Components - Props](https://vuejs.org/v2/guide/components-props.html)
  - [Components - `.sync` Modifier](https://vuejs.org/v2/guide/components-custom-events.html#sync-Modifier)

### v-model

- **Expects:** varies based on value of form inputs element or output of components

- **Limited to:**
  - `<input>`
  - `<select>`
  - `<textarea>`
  - components

- **Modifiers:**
  - [`.lazy`](https://vuejs.org/v2/guide/forms.html#lazy) - listen to `change` events instead of `input`
  - [`.number`](https://vuejs.org/v2/guide/forms.html#number) - cast valid input string to numbers
  - [`.trim`](https://vuejs.org/v2/guide/forms.html#trim) - trim input

- **Usage:**

  Create a two-way binding on a form input element or a component. For detailed usage and other notes, see the Guide section linked below.

- **Example:**

  ```html
  <!-- text input -->
  <input v-model="message" placeholder="edit me">
  <p>Message is: {{ message }}</p>

  <!-- multiline text -->
  <span>Multiline message is:</span>
  <p style="white-space: pre-line;">{{ message }}</p>
  <br>
  <textarea v-model="message" placeholder="add multiple lines"></textarea>

  <!-- checkbox -->
  <input type="checkbox" id="checkbox" v-model="checked">
  <label for="checkbox">{{ checked }}</label>

  <!-- multiple checkboxes bound to array -->
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames }}</span>

  <!-- radio -->
  <input type="radio" id="one" value="One" v-model="picked">
  <label for="one">One</label>
  <br>
  <input type="radio" id="two" value="Two" v-model="picked">
  <label for="two">Two</label>
  <br>
  <span>Picked: {{ picked }}</span>

  <!-- select -->
  <select v-model="selected">
    <option disabled value="">Please select one</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>

  <!-- multiple select -->
  <select v-model="selected" multiple>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <br>
  <span>Selected: {{ selected }}</span>

  <!-- with dynamic options -->
  <select v-model="selected">
    <option v-for="option in options" v-bind:value="option.value">
      {{ option.text }}
    </option>
  </select>
  <span>Selected: {{ selected }}</span>

  <!-- with modifiers -->
  <input v-model.lazy="msg">
  <input v-model.number="age" type="number">
  <input v-model.trim="msg">
  ```

- **See also:**
  - [Form Input Bindings](https://vuejs.org/v2/guide/forms.html)
  - [Components - Form Input Components using Custom Events](https://vuejs.org/v2/guide/components-custom-events.html#Customizing-Component-v-model)

### v-slot

- **Shorthand:** `#`

- **Expects:** JavaScript expression that is valid in a function argument position (supports destructuring in [supported environments](https://vuejs.org/v2/guide/components-slots.html#Destructuring-Slot-Props)). Optional - only needed if expecting props to be passed to the slot.

- **Argument:** slot name (optional, defaults to `default`)

- **Limited to:**
  - `<template>`
  - [components](https://vuejs.org/v2/guide/components-slots.html#Abbreviated-Syntax-for-Lone-Default-Slots) (for a lone default slot with props)

- **Usage:**

  Denote named slots or slots that expect to receive props.

- **Example:**

  ```html
  <!-- Named slots -->
  <base-layout>
    <template v-slot:header>
      <h1>About Me</h1>
    </template>

    Default slot content

    <template v-slot:footer>
      <p>Copyright 2016 Evan You</p>
    </template>
  </base-layout>

  <!-- Named slot that receives props -->
  <infinite-scroll>
    <template v-slot:item="slotProps">
      <div class="item">
        {{ slotProps.item.text }}
      </div>
    </template>
  </infinite-scroll>

  <!-- Default slot that receive props, with destructuring -->
  <mouse-position v-slot="{ x, y }">
    Mouse position: {{ x }}, {{ y }}
  </mouse-position>
  ```

  For more details, see the links below.

- **See also:**
  - [Components - Slots](https://vuejs.org/v2/guide/components-slots.html)
  - [RFC-0001](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md)

### v-pre

- **Does not expect expression**

- **Usage:**

  Skip compilation for this element and all its children. You can use this for displaying raw mustache tags. Skipping large numbers of nodes with no directives on them can also speed up compilation.

- **Example:**

  ```html
  <span v-pre>{{ this will not be compiled }}</span>
  ```

### v-cloak

- **Does not expect expression**

- **Usage:**

  This directive will remain on the element until the associated Vue instance finishes compilation. Combined with CSS rules such as `[v-cloak] { display: none }`, this directive can be used to hide un-compiled mustache bindings until the Vue instance is ready.

- **Example:**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```html
  <div v-cloak>
    {{ message }}
  </div>
  ```

  The `<div>` will not be visible until the compilation is done.

### v-once

- **Does not expect expression**

- **Details:**

  Render the element and component **only once**. On subsequent re-renders, the element/component and all of its children will be treated as static content and skipped. This can be used to optimize update performance.

- **Example:**

  ```html
  <!-- single element -->
  <span v-once>This will never change: {{msg}}</span>
  <!-- the element have children -->
  <div v-once>
    <h1>comment</h1>
    <p>{{msg}}</p>
  </div>
  <!-- component -->
  <my-component v-once :comment="msg"></my-component>
  <!-- `v-for` directive -->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

- **See also:**
  - [Data Binding Syntax - Interpolations](https://vuejs.org/v2/guide/syntax.html#Text)
  - [Components - Cheap Static Components with `v-once`](https://vuejs.org/v2/guide/components-edge-cases.html#Cheap-Static-Components-with-v-once)

## Special Attributes

### key

- **Expects:** `number | string | boolean (since 2.4.2) | symbol (since 2.5.12)`

  The `key` special attribute is primarily used as a hint for Vue's virtual DOM algorithm to identify VNodes when diffing the new list of nodes against the old list. Without keys, Vue uses an algorithm that minimizes element movement and tries to patch/reuse elements of the same type in-place as much as possible. With keys, it will reorder elements based on the order change of keys, and elements with keys that are no longer present will always be removed/destroyed.

  Children of the same common parent must have **unique keys**. Duplicate keys will cause render errors.

  The most common use case is combined with `v-for`:

  ```html
  <ul>
    <li v-for="item in items" :key="item.id">...</li>
  </ul>
  ```

  It can also be used to force replacement of an element/component instead of reusing it. This can be useful when you want to:

  - Properly trigger lifecycle hooks of a component
  - Trigger transitions

  For example:

  ```html
  <transition>
    <span :key="text">{{ text }}</span>
  </transition>
  ```

  When `text` changes, the `<span>` will always be replaced instead of patched, so a transition will be triggered.

### ref

- **Expects:** `string`

  `ref` is used to register a reference to an element or a child component. The reference will be registered under the parent component's `$refs` object. If used on a plain DOM element, the reference will be that element; if used on a child component, the reference will be component instance:

  ```html
  <!-- vm.$refs.p will be the DOM node -->
  <p ref="p">hello</p>

  <!-- vm.$refs.child will be the child component instance -->
  <child-component ref="child"></child-component>
  ```

  When used on elements/components with `v-for`, the registered reference will be an Array containing DOM nodes or component instances.

  An important note about the ref registration timing: because the refs themselves are created as a result of the render function, you cannot access them on the initial render - they don't exist yet! `$refs` is also non-reactive, therefore you should not attempt to use it in templates for data-binding.

- **See also:** [Child Component Refs](https://vuejs.org/v2/guide/components-edge-cases.html#Accessing-Child-Component-Instances-amp-Child-Elements)

### is

- **Expects:** `string | Object (component's options object)`

  Used for [dynamic components](https://vuejs.org/v2/guide/components-dynamic-async.html) and to work around [limitations of in-DOM templates](https://vuejs.org/v2/guide/components.html#DOM-Template-Parsing-Caveats).

  For example:

  ```html
  <!-- component changes when currentView changes -->
  <component v-bind:is="currentView"></component>

  <!-- necessary because `<my-row>` would be invalid inside -->
  <!-- a `<table>` element and so would be hoisted out      -->
  <table>
    <tr is="my-row"></tr>
  </table>
  ```

  For detailed usage, follow the links in the description above.

- **See also:** [Dynamic Components](https://vuejs.org/v2/guide/components-dynamic-async.html)

### slot (deprecated)

- **Expects:** `string`

- **Usage:**

  Used on content inserted into child components to indicate which named slot the content belongs to.

  **Note:** this attribute has been deprecated. Please use `v-slot` instead.

- **Example:**

  ```html
  <!-- old syntax using slot attribute -->
  <template slot="header">
    <h1>About Me</h1>
  </template>

  <!-- new syntax using v-slot -->
  <template v-slot:header>
    <h1>About Me</h1>
  </template>
  ```

### slot-scope (deprecated)

- **Expects:** `function argument expression`

- **Usage:**

  Used to denote an element or component as a scoped slot. The attribute value should be a valid JavaScript expression that can appear in the argument position of a function signature. This means in supported environments you can also use ES2015 destructuring in the expression. Serves as a replacement for [`scope`](#scope-replaced) in 2.5.0+.

  **Note:** this attribute has been deprecated. Please use `v-slot` with arguments instead.

- **Example:**

  ```html
  <!-- old syntax using slot-scope -->
  <div slot-scope="{ msg }">
    {{ msg }}
  </div>

  <!-- new syntax using v-slot -->
  <template v-slot:default="{ msg }">
    {{ msg }}
  </template>
  ```

## Built-in Components

### component

- **Props:**
  - `is` - string | Component | VNode

- **Usage:**

  A "meta component" for rendering dynamic components. The actual component to render is determined by the `is` prop. An `is` prop as a string could be either an HTML tag name or a Component name.

- **Example:**

  ```javascript
  // HTML tag
  new Vue({
    data: {
      view: 'div'
    },
    template: `
      <component :is="view"></component>
    `
  })

  // registered component
  new Vue({
    components: {
      'my-component': {
        template: '<div>A custom component!</div>'
      }
    },
    data: {
      view: 'my-component'
    },
    template: `
      <component :is="view"></component>
    `
  })

  // with props
  new Vue({
    components: {
      post: {
        props: ['title'],
        template: '<h3>{{ title }}</h3>'
      }
    },
    data: {
      posts: [
        { id: 1, title: 'My journey with Vue' },
        { id: 2, title: 'Blogging with Vue' },
        { id: 3, title: 'Why Vue is so fun' }
      ]
    },
    template: `
      <div>
        <component
          v-for="post in posts"
          :key="post.id"
          :is="'post'"
          :title="post.title"
        ></component>
      </div>
    `
  })
  ```

- **See also:** [Dynamic Components](https://vuejs.org/v2/guide/components-dynamic-async.html)

### transition

- **Props:**
  - `name` - string, Used to automatically generate transition CSS class names. e.g. `name: 'fade'` will auto expand to `.fade-enter`, `.fade-enter-active`, etc.
  - `appear` - boolean, Whether to apply transition on initial render. Defaults to `false`.
  - `css` - boolean, Whether to apply CSS transition classes. Defaults to `true`. If set to `false`, will only trigger JavaScript hooks registered via component events.
  - `type` - string, Specifies the type of transition events to wait for to determine transition end timing. Available values are `"transition"` and `"animation"`. By default, it will automatically detect the type that has a longer duration.
  - `mode` - string, Controls the timing sequence of leaving/entering transitions. Available modes are `"out-in"` and `"in-out"`; defaults to simultaneous.
  - `duration` - number | { enter: number, leave: number }. Specifies the duration of transition. By default, Vue waits for the first `transitionend` or `animationend` event on the root transition element.
  - `enter-class` - string
  - `leave-class` - string
  - `appear-class` - string
  - `enter-to-class` - string
  - `leave-to-class` - string
  - `appear-to-class` - string
  - `enter-active-class` - string
  - `leave-active-class` - string
  - `appear-active-class` - string

- **Events:**
  - `before-enter`
  - `before-leave`
  - `before-appear`
  - `enter`
  - `leave`
  - `appear`
  - `after-enter`
  - `after-leave`
  - `after-appear`
  - `enter-cancelled`
  - `leave-cancelled` (`v-show` only)
  - `appear-cancelled`

- **Usage:**

  `<transition>` serve as transition effects for **single** element/component. The `<transition>` only applies the transition behavior to the wrapped content inside; it doesn't render an extra DOM element, or show up in the inspected component hierarchy.

- **Example:**

  ```html
  <!-- simple element -->
  <transition>
    <div v-if="ok">toggled content</div>
  </transition>

  <!-- dynamic component -->
  <transition name="fade" mode="out-in" appear>
    <component :is="view"></component>
  </transition>

  <!-- event hooking -->
  <div id="transition-demo">
    <transition @after-enter="transitionComplete">
      <div v-show="ok">toggled content</div>
    </transition>
  </div>
  ```

  ```javascript
  new Vue({
    el: '#transition-demo',
    data: {
      ok: true
    },
    methods: {
      transitionComplete: function (el) {
        // for passed 'el' that DOM element as the argument, something ...
      }
    }
  })
  ```

  CSS transition classes:

  ```css
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
  .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
  }
  ```

- **See also:** [Transitions: Entering, Leaving, and Lists](https://vuejs.org/v2/guide/transitions.html)

### transition-group

- **Props:**
  - `tag` - string, defaults to `span`.
  - `move-class` - overwrite CSS class applied during moving transition.
  - exposes the same props as `<transition>` except `mode`.

- **Events:**
  - exposes the same events as `<transition>`.

- **Usage:**

  `<transition-group>` serve as transition effects for **multiple** elements/components. The `<transition-group>` renders a real DOM element. By default it renders a `<span>`, and you can configure what element it should render via the `tag` attribute.

  Note that every child in a `<transition-group>` must be **uniquely keyed** for the animations to work properly.

  `<transition-group>` supports moving transitions via CSS transform. When a child's position on screen has changed after an update, it will get applied a moving CSS class (auto generated from the `name` attribute or configured with the `move-class` attribute). If the CSS `transform` property is "transition-able" when the moving class is applied, the element will be smoothly animated to its destination using the [FLIP technique](https://aerotwist.com/blog/flip-your-animations/).

- **Example:**

  ```html
  <transition-group name="list" tag="ul">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </transition-group>
  ```

  ```css
  .list-enter-active, .list-leave-active {
    transition: all 1s;
  }
  .list-enter, .list-leave-to /* .list-leave-active below version 2.1.8 */ {
    opacity: 0;
    transform: translateY(30px);
  }
  .list-move {
    transition: transform 1s;
  }
  ```

- **See also:** [Transitions: Entering, Leaving, and Lists](https://vuejs.org/v2/guide/transitions.html)

### keep-alive

- **Props:**
  - `include` - string | RegExp | Array. Only components with matching names will be cached.
  - `exclude` - string | RegExp | Array. Any component with a matching name will not be cached.
  - `max` - number | string. The maximum number of component instances to cache.

- **Usage:**

  When wrapped around a dynamic component, `<keep-alive>` caches the inactive component instances without destroying them. Similar to `<transition>`, `<keep-alive>` is an abstract component: it doesn't render a DOM element itself, and doesn't show up in the component parent chain.

  When a component inside `<keep-alive>` is toggled, its `activated` and `deactivated` lifecycle hooks will be invoked accordingly.

  > In 2.2.0+ and above, `activated` and `deactivated` will fire for all nested components inside a `<keep-alive>` tree.

  Primarily used to preserve component state or avoid re-rendering.

- **Example:**

  Basic usage:

  ```html
  <!-- basic -->
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>

  <!-- multiple conditional children -->
  <keep-alive>
    <comp-a v-if="a > 1"></comp-a>
    <comp-b v-else></comp-b>
  </keep-alive>

  <!-- used together with `<transition>` -->
  <transition>
    <keep-alive>
      <component :is="view"></component>
    </keep-alive>
  </transition>
  ```

  Using `include` and `exclude`:

  ```html
  <!-- comma-delimited string -->
  <keep-alive include="a,b">
    <component :is="view"></component>
  </keep-alive>

  <!-- regex (use `v-bind`) -->
  <keep-alive :include="/a|b/">
    <component :is="view"></component>
  </keep-alive>

  <!-- Array (use `v-bind`) -->
  <keep-alive :include="['a', 'b']">
    <component :is="view"></component>
  </keep-alive>
  ```

  Using `max`:

  ```html
  <keep-alive :max="10">
    <component :is="view"></component>
  </keep-alive>
  ```

  Note: `<keep-alive>` does not work with functional components because they do not have instances to be cached.

- **See also:** [Dynamic Components - keep-alive](https://vuejs.org/v2/guide/components-dynamic-async.html#keep-alive-with-Dynamic-Components)

### slot

- **Props:**
  - `name` - string, Used for named slot.

- **Usage:**

  `<slot>` serve as content distribution outlets in component templates. `<slot>` itself will be replaced.

  For detailed usage, see the guide section linked below.

- **Example:**

  ```html
  <!-- in component template -->
  <div>
    <h2>I'm a child component</h2>
    <slot>
      This will only be displayed if there is no content
      to be distributed.
    </slot>
  </div>

  <!-- parent component using the child component -->
  <my-component>
    <p>This is some original content</p>
    <p>This is some more original content</p>
  </my-component>

  <!-- rendered result -->
  <div>
    <h2>I'm a child component</h2>
    <p>This is some original content</p>
    <p>This is some more original content</p>
  </div>
  ```

  Named slots:

  ```html
  <!-- component template -->
  <div class="container">
    <header>
      <slot name="header"></slot>
    </header>
    <main>
      <slot></slot>
    </main>
    <footer>
      <slot name="footer"></slot>
    </footer>
  </div>

  <!-- parent component -->
  <app-layout>
    <template v-slot:header>
      <h1>Here might be a page title</h1>
    </template>

    <p>A paragraph for the main content.</p>
    <p>And another one.</p>

    <template v-slot:footer>
      <p>Here's some contact info</p>
    </template>
  </app-layout>
  ```

- **See also:** [Content Distribution with Slots](https://vuejs.org/v2/guide/components-slots.html)

## VNode Interface

- **Details:**

  The VNode interface in Vue.js represents a virtual DOM node. The virtual DOM is Vue's way of representing the actual DOM in JavaScript. VNodes are lightweight JavaScript objects that describe what the DOM should look like.

  ```typescript
  interface VNode {
    tag?: string;
    data?: VNodeData;
    children?: VNodeChildren;
    text?: string;
    elm?: Node;
    ns?: string;
    context?: Vue;
    key?: string | number;
    componentOptions?: VNodeComponentOptions;
    componentInstance?: Vue;
    parent?: VNode;
    raw?: boolean;
    isStatic?: boolean;
    isRootInsert: boolean;
    isComment: boolean;
  }

  interface VNodeData {
    key?: string | number;
    slot?: string;
    scopedSlots?: { [key: string]: ScopedSlot | undefined };
    ref?: string;
    refInFor?: boolean;
    tag?: string;
    staticClass?: string;
    class?: any;
    staticStyle?: { [key: string]: any };
    style?: object[] | object;
    props?: { [key: string]: any };
    attrs?: { [key: string]: any };
    domProps?: { [key: string]: any };
    hook?: { [key: string]: Function };
    on?: { [key: string]: Function | Function[] };
    nativeOn?: { [key: string]: Function | Function[] };
    transition?: object;
    show?: boolean;
    inlineTemplate?: {
      render: Function;
      staticRenderFns: Function[];
    };
    directives?: VNodeDirective[];
    keepAlive?: boolean;
  }

  interface VNodeDirective {
    name: string;
    value?: any;
    oldValue?: any;
    expression?: string;
    arg?: string;
    modifiers?: { [key: string]: boolean };
  }
  ```

- **See also:** [Render Function](https://vuejs.org/v2/guide/render-function.html)

## Server-Side Rendering

- **Details:**

  Vue.js supports server-side rendering (SSR), which allows you to render your Vue components on the server and send the rendered HTML to the client. This can improve initial load performance and SEO.

  The key APIs for SSR are:

  - `createRenderer()` - Creates a server renderer instance
  - `renderToString()` - Renders a Vue instance to HTML string
  - `renderToStream()` - Renders a Vue instance to a Node.js stream

- **Example:**

  ```javascript
  // Step 1: Create a Vue instance
  const Vue = require('vue')
  const app = new Vue({
    template: `<div>Hello World</div>`
  })

  // Step 2: Create a renderer
  const renderer = require('vue-server-renderer').createRenderer()

  // Step 3: Render the Vue instance to HTML
  renderer.renderToString(app, (err, html) => {
    if (err) throw err
    console.log(html)
    // => <div data-server-rendered="true">Hello World</div>
  })

  // or using Promises:
  renderer.renderToString(app).then(html => {
    console.log(html)
  }).catch(err => {
    console.error(err)
  })
  ```

- **See also:** [Server-Side Rendering Guide](https://ssr.vuejs.org/)