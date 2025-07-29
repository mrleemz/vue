# Vue.js 2.5.21 - Comprehensive API Documentation

## Table of Contents

1. [Vue Constructor](#vue-constructor)
2. [Vue Static Methods](#vue-static-methods)
3. [Vue Global Configuration](#vue-global-configuration)
4. [Vue Instance Properties](#vue-instance-properties)
5. [Vue Instance Methods](#vue-instance-methods)
6. [Component Options](#component-options)
7. [Lifecycle Hooks](#lifecycle-hooks)
8. [Custom Directives](#custom-directives)
9. [Functional Components](#functional-components)
10. [Built-in Components](#built-in-components)
11. [TypeScript Support](#typescript-support)
12. [Examples and Usage Patterns](#examples-and-usage-patterns)

---

## Vue Constructor

### `new Vue(options)`

The Vue constructor creates a new Vue instance. It must be called with the `new` keyword.

**Parameters:**
- `options` (Object): Component options object

**Returns:** Vue instance

**Example:**
```javascript
// Basic Vue instance
const vm = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  },
  methods: {
    greet() {
      console.log('Hello from Vue!')
    }
  }
})

// Using all major options
const vm = new Vue({
  el: '#app',
  data() {
    return {
      count: 0,
      items: []
    }
  },
  computed: {
    doubleCount() {
      return this.count * 2
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  created() {
    console.log('Component created!')
  },
  mounted() {
    console.log('Component mounted!')
  }
})
```

---

## Vue Static Methods

### `Vue.extend(options)`

Creates a Vue subclass with extended functionality.

**Parameters:**
- `options` (Object): Component options object

**Returns:** Vue constructor function

**Example:**
```javascript
// Create a reusable component constructor
const MyComponent = Vue.extend({
  template: '<div>{{message}}</div>',
  data() {
    return {
      message: 'Hello from extended component!'
    }
  }
})

// Use the constructor
const instance = new MyComponent().$mount('#app')
```

### `Vue.nextTick(callback?, context?)`

Defer the callback to be executed after the next DOM update cycle.

**Parameters:**
- `callback` (Function, optional): Callback function
- `context` (Object, optional): Context for the callback

**Returns:** Promise (if no callback provided)

**Example:**
```javascript
// With callback
Vue.nextTick(() => {
  // DOM has been updated
  console.log('DOM updated')
})

// With Promise
Vue.nextTick().then(() => {
  console.log('DOM updated')
})

// In component context
this.$nextTick(() => {
  // Access updated DOM
  this.$refs.myElement.focus()
})
```

### `Vue.set(target, propertyName/index, value)`

Adds a property to a reactive object, ensuring the new property is reactive.

**Parameters:**
- `target` (Object|Array): Target object or array
- `propertyName/index` (String|Number): Property name or array index
- `value` (Any): Value to set

**Returns:** The set value

**Example:**
```javascript
// Adding reactive property to object
const obj = { a: 1 }
Vue.set(obj, 'b', 2)
// obj is now { a: 1, b: 2 } and 'b' is reactive

// Setting array item
const arr = ['a', 'b', 'c']
Vue.set(arr, 1, 'x')
// arr is now ['a', 'x', 'c']

// In component
this.$set(this.someObject, 'newProperty', 'newValue')
```

### `Vue.delete(target, propertyName/index)`

Deletes a property from a reactive object, ensuring the deletion triggers view updates.

**Parameters:**
- `target` (Object|Array): Target object or array
- `propertyName/index` (String|Number): Property name or array index

**Example:**
```javascript
// Deleting reactive property
const obj = { a: 1, b: 2 }
Vue.delete(obj, 'b')
// obj is now { a: 1 }

// Deleting array item
const arr = ['a', 'b', 'c']
Vue.delete(arr, 1)
// arr is now ['a', 'c']

// In component
this.$delete(this.someObject, 'unwantedProperty')
```

### `Vue.directive(id, definition?)`

Register or retrieve a global directive.

**Parameters:**
- `id` (String): Directive ID
- `definition` (Function|Object, optional): Directive definition

**Returns:** Directive definition (if only ID provided) or Vue constructor

**Example:**
```javascript
// Register a directive
Vue.directive('focus', {
  inserted(el) {
    el.focus()
  }
})

// Function directive shorthand
Vue.directive('highlight', function(el, binding) {
  el.style.backgroundColor = binding.value
})

// Use in template
// <input v-focus>
// <div v-highlight="'yellow'">Highlighted text</div>
```

### `Vue.filter(id, definition?)`

Register or retrieve a global filter.

**Parameters:**
- `id` (String): Filter ID
- `definition` (Function, optional): Filter function

**Returns:** Filter function (if only ID provided) or Vue constructor

**Example:**
```javascript
// Register a filter
Vue.filter('capitalize', function(value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

// Use in template
// {{ message | capitalize }}
// <div v-bind:id="rawId | formatId"></div>
```

### `Vue.component(id, definition?)`

Register or retrieve a global component.

**Parameters:**
- `id` (String): Component ID
- `definition` (Function|Object, optional): Component definition

**Returns:** Component constructor (if only ID provided) or Vue constructor

**Example:**
```javascript
// Register a component
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})

// Register with extended constructor
Vue.component('my-other-component', Vue.extend({
  template: '<div>Another component!</div>'
}))

// Use in template
// <my-component></my-component>
```

### `Vue.use(plugin, options?)`

Install a Vue.js plugin.

**Parameters:**
- `plugin` (Object|Function): Plugin to install
- `options` (Object, optional): Plugin options

**Returns:** Vue constructor

**Example:**
```javascript
// Install a plugin
Vue.use(MyPlugin)

// Install with options
Vue.use(MyPlugin, {
  option1: 'value1',
  option2: 'value2'
})

// Plugin definition
const MyPlugin = {
  install(Vue, options) {
    // Add global method
    Vue.myGlobalMethod = function() {
      // Some logic
    }
    
    // Add instance method
    Vue.prototype.$myMethod = function() {
      // Some logic
    }
  }
}
```

### `Vue.mixin(mixin)`

Apply a mixin globally, affecting every Vue instance created afterwards.

**Parameters:**
- `mixin` (Object): Mixin object

**Returns:** Vue constructor

**Example:**
```javascript
// Global mixin
Vue.mixin({
  created() {
    const myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

// Now every component will have this behavior
new Vue({
  myOption: 'hello!'
})
```

### `Vue.compile(template)`

Compiles a template string into a render function.

**Parameters:**
- `template` (String): Template string to compile

**Returns:** Object with render and staticRenderFns

**Example:**
```javascript
const res = Vue.compile('<div><span>{{ msg }}</span></div>')

new Vue({
  data: {
    msg: 'hello'
  },
  render: res.render,
  staticRenderFns: res.staticRenderFns
})
```

---

## Vue Global Configuration

### `Vue.config`

Global configuration object that contains Vue's global configurations.

#### `Vue.config.silent`
- **Type:** Boolean
- **Default:** false
- **Usage:** Suppress all Vue logs and warnings

```javascript
Vue.config.silent = true
```

#### `Vue.config.optionMergeStrategies`
- **Type:** Object
- **Default:** {}
- **Usage:** Define custom merge strategies for custom options

```javascript
Vue.config.optionMergeStrategies.myOption = function(toVal, fromVal) {
  // Return merged value
  return toVal || fromVal
}
```

#### `Vue.config.devtools`
- **Type:** Boolean
- **Default:** true (in development), false (in production)
- **Usage:** Configure whether to allow vue-devtools inspection

```javascript
Vue.config.devtools = true
```

#### `Vue.config.errorHandler`
- **Type:** Function
- **Default:** undefined
- **Usage:** Assign a handler for uncaught errors during component render and watchers

```javascript
Vue.config.errorHandler = function(err, vm, info) {
  console.error('Vue error:', err)
  console.log('Component:', vm)
  console.log('Error info:', info)
}
```

#### `Vue.config.warnHandler`
- **Type:** Function
- **Default:** undefined
- **Usage:** Assign a custom handler for Vue warnings

```javascript
Vue.config.warnHandler = function(msg, vm, trace) {
  console.warn('Vue warning:', msg)
  console.log('Component:', vm)
  console.log('Trace:', trace)
}
```

#### `Vue.config.ignoredElements`
- **Type:** Array<String | RegExp>
- **Default:** []
- **Usage:** Make Vue ignore custom elements defined outside of Vue

```javascript
Vue.config.ignoredElements = [
  'my-custom-web-component',
  'another-web-component',
  /^ion-/
]
```

#### `Vue.config.keyCodes`
- **Type:** Object
- **Default:** {}
- **Usage:** Define custom key alias(es) for v-on

```javascript
Vue.config.keyCodes = {
  v: 86,
  f1: 112,
  mediaPlayPause: 179,
  up: [38, 87]
}
```

---

## Vue Instance Properties

All Vue instance properties are prefixed with `$` to differentiate them from user-defined properties.

### `vm.$data`
- **Type:** Object
- **Read only:** false
- **Details:** The data object that the Vue instance is observing

```javascript
const vm = new Vue({
  data: { a: 1 }
})

console.log(vm.$data) // { a: 1 }
vm.$data.a = 2
console.log(vm.a) // 2
```

### `vm.$props`
- **Type:** Object
- **Read only:** true
- **Details:** An object representing the current props a component has received

```javascript
// In child component
export default {
  props: ['message'],
  mounted() {
    console.log(this.$props) // { message: "Hello" }
  }
}
```

### `vm.$el`
- **Type:** Element
- **Read only:** true
- **Details:** The root DOM element that the Vue instance is managing

```javascript
const vm = new Vue({
  el: '#app'
})

console.log(vm.$el) // <div id="app">...</div>
```

### `vm.$options`
- **Type:** Object
- **Read only:** true
- **Details:** The instantiation options used for the current Vue instance

```javascript
const vm = new Vue({
  customOption: 'foo',
  created() {
    console.log(this.$options.customOption) // 'foo'
  }
})
```

### `vm.$parent`
- **Type:** Vue instance
- **Read only:** true
- **Details:** The parent instance, if the current instance has one

```javascript
// In child component
this.$parent.$data.parentProperty = 'new value'
```

### `vm.$root`
- **Type:** Vue instance
- **Read only:** true
- **Details:** The root Vue instance of the current component tree

```javascript
// Access root instance from any component
this.$root.$data.globalProperty = 'value'
```

### `vm.$children`
- **Type:** Array<Vue instance>
- **Read only:** true
- **Details:** The direct child components of the current instance

```javascript
// Access child components
this.$children.forEach(child => {
  console.log(child.$options.name)
})
```

### `vm.$slots`
- **Type:** Object
- **Read only:** true
- **Details:** An object of VNode arrays representing the slots distributed by the parent

```javascript
// In component template
// <slot name="header"></slot>
// <slot></slot> (default slot)

// Access slots in component
export default {
  mounted() {
    console.log(this.$slots.default) // VNodes for default slot
    console.log(this.$slots.header)  // VNodes for header slot
  }
}
```

### `vm.$scopedSlots`
- **Type:** Object
- **Read only:** true
- **Details:** An object of functions, each returning VNodes for scoped slots

```javascript
// In component
export default {
  mounted() {
    console.log(this.$scopedSlots.default({
      user: { name: 'John' }
    }))
  }
}
```

### `vm.$refs`
- **Type:** Object
- **Read only:** true
- **Details:** An object of DOM elements and component instances registered with ref attributes

```javascript
// Template: <input ref="input">
// Template: <child-component ref="child"></child-component>

// Access refs
this.$refs.input.focus()
this.$refs.child.someMethod()
```

### `vm.$isServer`
- **Type:** Boolean
- **Read only:** true
- **Details:** Whether the current Vue instance is running on the server

```javascript
if (this.$isServer) {
  // Server-side rendering logic
} else {
  // Client-side logic
}
```

### `vm.$attrs`
- **Type:** Object
- **Read only:** true
- **Details:** Contains parent-scope attribute bindings that are not recognized as props

```javascript
// Useful for higher-order components
export default {
  inheritAttrs: false,
  template: '<div><child v-bind="$attrs"></child></div>'
}
```

### `vm.$listeners`
- **Type:** Object
- **Read only:** true
- **Details:** Contains parent-scope v-on event listeners

```javascript
// Forward all listeners to child component
export default {
  template: '<child v-on="$listeners"></child>'
}
```

---

## Vue Instance Methods

### Data Methods

#### `vm.$watch(expOrFn, callback, options?)`

Watch an expression or a computed function on the Vue instance for changes.

**Parameters:**
- `expOrFn` (String|Function): Expression or function to watch
- `callback` (Function): Callback function
- `options` (Object, optional): Watch options

**Returns:** Function (unwatch function)

**Example:**
```javascript
// Watch a data property
const unwatch = this.$watch('message', function(newVal, oldVal) {
  console.log('Message changed from', oldVal, 'to', newVal)
})

// Watch with deep option
this.$watch('user', function(newVal, oldVal) {
  console.log('User object changed')
}, { deep: true })

// Watch a computed function
this.$watch(
  function() {
    return this.firstName + ' ' + this.lastName
  },
  function(newName, oldName) {
    console.log('Full name changed to', newName)
  }
)

// Unwatch
unwatch()
```

#### `vm.$set(target, propertyName, value)`

Alias for `Vue.set`. Adds a property to a reactive object.

**Example:**
```javascript
this.$set(this.user, 'age', 25)
this.$set(this.items, 0, 'new item')
```

#### `vm.$delete(target, propertyName)`

Alias for `Vue.delete`. Deletes a property from a reactive object.

**Example:**
```javascript
this.$delete(this.user, 'age')
this.$delete(this.items, 0)
```

### Event Methods

#### `vm.$on(event, callback)`

Listen for a custom event on the current vm.

**Parameters:**
- `event` (String|Array): Event name(s)
- `callback` (Function): Event handler

**Returns:** vm

**Example:**
```javascript
// Single event
this.$on('custom-event', function(data) {
  console.log('Custom event received:', data)
})

// Multiple events
this.$on(['event1', 'event2'], function(data) {
  console.log('Event received:', data)
})
```

#### `vm.$once(event, callback)`

Listen for a custom event, but only once.

**Parameters:**
- `event` (String): Event name
- `callback` (Function): Event handler

**Returns:** vm

**Example:**
```javascript
this.$once('custom-event', function(data) {
  console.log('This will only fire once:', data)
})
```

#### `vm.$off(event?, callback?)`

Remove custom event listener(s).

**Parameters:**
- `event` (String|Array, optional): Event name(s)
- `callback` (Function, optional): Specific handler to remove

**Returns:** vm

**Example:**
```javascript
// Remove all listeners
this.$off()

// Remove all listeners for specific event
this.$off('custom-event')

// Remove specific listener
this.$off('custom-event', onCustomEvent)

// Remove listeners for multiple events
this.$off(['event1', 'event2'])
```

#### `vm.$emit(eventName, ...args)`

Trigger an event on the current instance.

**Parameters:**
- `eventName` (String): Event name
- `...args` (Any): Arguments to pass to event handlers

**Returns:** vm

**Example:**
```javascript
// Emit event without data
this.$emit('custom-event')

// Emit event with data
this.$emit('user-updated', { id: 1, name: 'John' })

// Emit event with multiple arguments
this.$emit('data-changed', newValue, oldValue, timestamp)
```

### Lifecycle Methods

#### `vm.$mount(elementOrSelector?)`

Manually mount a Vue instance.

**Parameters:**
- `elementOrSelector` (Element|String, optional): DOM element or CSS selector

**Returns:** vm

**Example:**
```javascript
// Mount to specific element
const vm = new Vue({
  template: '<div>Hello!</div>'
})
vm.$mount('#app')

// Mount without target (returns DocumentFragment)
const vm = new Vue({
  template: '<div>Hello!</div>'
})
const element = vm.$mount().$el
document.body.appendChild(element)
```

#### `vm.$forceUpdate()`

Force the Vue instance to re-render.

**Example:**
```javascript
// Force component to re-render
this.$forceUpdate()
```

#### `vm.$nextTick(callback?)`

Defer the callback to be executed after the next DOM update cycle.

**Parameters:**
- `callback` (Function, optional): Callback function

**Returns:** Promise (if no callback provided)

**Example:**
```javascript
// With callback
this.$nextTick(function() {
  // DOM updated
  this.$refs.input.focus()
})

// With Promise
this.$nextTick().then(() => {
  // DOM updated
  this.$refs.input.focus()
})
```

#### `vm.$destroy()`

Completely destroy a vm instance.

**Example:**
```javascript
// Destroy component instance
this.$destroy()
```

---

## Component Options

Component options define the behavior and appearance of Vue components.

### Data Options

#### `data`
- **Type:** Object | Function
- **Details:** The data object for the Vue instance

```javascript
// Object (only for root instance)
new Vue({
  data: {
    message: 'Hello'
  }
})

// Function (required for components)
export default {
  data() {
    return {
      message: 'Hello',
      count: 0
    }
  }
}
```

#### `props`
- **Type:** Array<String> | Object
- **Details:** A list/hash of attributes that are exposed to accept data from the parent component

```javascript
// Array syntax
export default {
  props: ['message', 'likes', 'isPublished']
}

// Object syntax with validation
export default {
  props: {
    message: String,
    likes: {
      type: Number,
      default: 0,
      required: true,
      validator(value) {
        return value >= 0
      }
    },
    author: {
      type: Object,
      default() {
        return { name: 'Anonymous' }
      }
    }
  }
}
```

#### `computed`
- **Type:** Object
- **Details:** Computed properties to be mixed into the Vue instance

```javascript
export default {
  data() {
    return {
      firstName: 'John',
      lastName: 'Doe'
    }
  },
  computed: {
    // Basic computed property
    fullName() {
      return this.firstName + ' ' + this.lastName
    },
    
    // Computed with getter and setter
    fullNameWithSetter: {
      get() {
        return this.firstName + ' ' + this.lastName
      },
      set(value) {
        const names = value.split(' ')
        this.firstName = names[0]
        this.lastName = names[names.length - 1]
      }
    }
  }
}
```

#### `methods`
- **Type:** Object
- **Details:** Methods to be mixed into the Vue instance

```javascript
export default {
  methods: {
    greet() {
      console.log('Hello!')
    },
    sayHello(name) {
      console.log(`Hello, ${name}!`)
    },
    async fetchData() {
      try {
        const response = await fetch('/api/data')
        return await response.json()
      } catch (error) {
        console.error('Failed to fetch data:', error)
      }
    }
  }
}
```

#### `watch`
- **Type:** Object
- **Details:** An object where keys are expressions to watch and values are the corresponding callbacks

```javascript
export default {
  data() {
    return {
      question: '',
      answer: 'I cannot give you an answer until you ask a question!'
    }
  },
  watch: {
    // Basic watcher
    question(newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    },
    
    // Watcher with options
    '$route': {
      handler(to, from) {
        console.log('Route changed')
      },
      immediate: true,
      deep: true
    },
    
    // Multiple handlers
    someData: [
      'handle1',
      function handle2(val, oldVal) { /* ... */ },
      {
        handler: function handle3(val, oldVal) { /* ... */ },
        deep: true
      }
    ]
  }
}
```

### DOM Options

#### `el`
- **Type:** String | Element
- **Details:** Provide the Vue instance an existing DOM element to mount on

```javascript
new Vue({
  el: '#app',
  // or
  el: document.getElementById('app')
})
```

#### `template`
- **Type:** String
- **Details:** A string template to be used as the markup for the Vue instance

```javascript
export default {
  template: `
    <div>
      <h1>{{ title }}</h1>
      <p>{{ message }}</p>
    </div>
  `,
  data() {
    return {
      title: 'Hello Vue!',
      message: 'This is a template'
    }
  }
}
```

#### `render`
- **Type:** Function
- **Details:** A render function that returns a Virtual Node

```javascript
export default {
  render(createElement) {
    return createElement('div', {
      attrs: {
        id: 'my-div'
      }
    }, [
      createElement('h1', 'Hello Vue!'),
      createElement('p', 'Rendered with render function')
    ])
  }
}

// Using JSX (with babel-plugin-transform-vue-jsx)
export default {
  render() {
    return (
      <div id="my-div">
        <h1>Hello Vue!</h1>
        <p>Rendered with JSX</p>
      </div>
    )
  }
}
```

### Asset Options

#### `directives`
- **Type:** Object
- **Details:** A hash of directives to be made available to the Vue instance

```javascript
export default {
  directives: {
    focus: {
      inserted(el) {
        el.focus()
      }
    },
    highlight: function(el, binding) {
      el.style.backgroundColor = binding.value
    }
  }
}
```

#### `filters`
- **Type:** Object
- **Details:** A hash of filters to be made available to the Vue instance

```javascript
export default {
  filters: {
    capitalize(value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    },
    currencyFormat(value) {
      return '$' + value.toFixed(2)
    }
  }
}
```

#### `components`
- **Type:** Object
- **Details:** A hash of components to be made available to the Vue instance

```javascript
import ChildComponent from './ChildComponent.vue'

export default {
  components: {
    ChildComponent,
    'my-component': {
      template: '<div>A local component</div>'
    }
  }
}
```

### Composition Options

#### `parent`
- **Type:** Vue instance
- **Details:** Specify the parent instance for the instance to be created

```javascript
const parent = new Vue({ /* ... */ })
const child = new Vue({
  parent: parent,
  // ...
})
```

#### `mixins`
- **Type:** Array<Object>
- **Details:** An array of mixin objects

```javascript
const myMixin = {
  created() {
    this.hello()
  },
  methods: {
    hello() {
      console.log('Hello from mixin!')
    }
  }
}

export default {
  mixins: [myMixin],
  created() {
    console.log('Component created')
  }
}
```

#### `extends`
- **Type:** Object | Function
- **Details:** Allows declaratively extending another component

```javascript
const CompA = { /* ... */ }

const CompB = {
  extends: CompA,
  // ...
}
```

### Other Options

#### `name`
- **Type:** String
- **Details:** Allow the component to recursively invoke itself in its template

```javascript
export default {
  name: 'MyComponent',
  template: `
    <div>
      <my-component v-if="condition"></my-component>
    </div>
  `
}
```

#### `delimiters`
- **Type:** Array<String>
- **Default:** ["{{", "}}"]
- **Details:** Change the plain text interpolation delimiters

```javascript
export default {
  delimiters: ['${', '}'],
  template: '<div>${ message }</div>'
}
```

#### `functional`
- **Type:** Boolean
- **Details:** Make component functional (stateless and instanceless)

```javascript
export default {
  functional: true,
  render(h, { props, children, parent, data }) {
    return h('div', data, children)
  }
}
```

#### `model`
- **Type:** Object
- **Details:** Customize the prop and event used when using v-model

```javascript
export default {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean
  }
}
```

#### `inheritAttrs`
- **Type:** Boolean
- **Default:** true
- **Details:** Control whether to inherit parent attributes

```javascript
export default {
  inheritAttrs: false,
  props: ['label'],
  template: `
    <label>
      {{ label }}
      <input v-bind="$attrs" v-on="$listeners">
    </label>
  `
}
```

#### `comments`
- **Type:** Boolean
- **Default:** false
- **Details:** Preserve HTML comments in templates

```javascript
export default {
  comments: true,
  template: `
    <div>
      <!-- This comment will be preserved -->
      <p>Content</p>
    </div>
  `
}
```

---

## Lifecycle Hooks

Vue components have a series of lifecycle hooks that allow you to run code at specific stages.

### `beforeCreate`
Called synchronously immediately after the instance has been initialized, before data observation and event/watcher setup.

```javascript
export default {
  beforeCreate() {
    console.log('beforeCreate')
    // Data and events are not yet available
    console.log(this.message) // undefined
  },
  data() {
    return {
      message: 'Hello'
    }
  }
}
```

### `created`
Called synchronously after the instance is created. Data observation, computed properties, methods, watch/event callbacks have been set up.

```javascript
export default {
  created() {
    console.log('created')
    // Data is available
    console.log(this.message) // 'Hello'
    
    // DOM is not yet available
    console.log(this.$el) // undefined
    
    // Good place for API calls
    this.fetchData()
  },
  methods: {
    fetchData() {
      // Fetch initial data
    }
  }
}
```

### `beforeMount`
Called right before the mounting begins: the render function is about to be called for the first time.

```javascript
export default {
  beforeMount() {
    console.log('beforeMount')
    // Template has been compiled
    // DOM has not been rendered yet
  }
}
```

### `mounted`
Called after the instance has been mounted, where `el` is replaced by the newly created `vm.$el`.

```javascript
export default {
  mounted() {
    console.log('mounted')
    // DOM is available
    console.log(this.$el) // DOM element
    
    // Good place for DOM manipulation
    this.$refs.input.focus()
    
    // Initialize third-party libraries
    this.initializeMap()
  },
  methods: {
    initializeMap() {
      // Initialize map library
    }
  }
}
```

### `beforeUpdate`
Called when data changes, before the DOM is re-rendered and patched.

```javascript
export default {
  beforeUpdate() {
    console.log('beforeUpdate')
    // Good place to access existing DOM before update
    const oldValue = this.$el.textContent
  }
}
```

### `updated`
Called after a data change causes the virtual DOM to be re-rendered and patched.

```javascript
export default {
  updated() {
    console.log('updated')
    // DOM has been updated
    // Avoid changing data here as it may cause infinite update loop
  }
}
```

### `activated`
Called when a kept-alive component is activated.

```javascript
export default {
  activated() {
    console.log('activated')
    // Component was activated from keep-alive cache
    // Good place to fetch fresh data
    this.refreshData()
  }
}
```

### `deactivated`
Called when a kept-alive component is deactivated.

```javascript
export default {
  deactivated() {
    console.log('deactivated')
    // Component was deactivated and cached
    // Good place to cleanup timers, cancel requests
    this.cleanup()
  }
}
```

### `beforeDestroy`
Called right before a Vue instance is destroyed.

```javascript
export default {
  beforeDestroy() {
    console.log('beforeDestroy')
    // Instance is still fully functional
    // Good place for cleanup
    this.cleanup()
  },
  methods: {
    cleanup() {
      // Clear timers, remove event listeners, etc.
      clearInterval(this.timer)
      window.removeEventListener('resize', this.handleResize)
    }
  }
}
```

### `destroyed`
Called after a Vue instance has been destroyed.

```javascript
export default {
  destroyed() {
    console.log('destroyed')
    // All directives have been unbound
    // All event listeners have been removed
    // All child Vue instances have been destroyed
  }
}
```

### `errorCaptured`
Called when an error from any descendent component is captured.

```javascript
export default {
  errorCaptured(err, vm, info) {
    console.log('Error captured:', err)
    console.log('Component:', vm)
    console.log('Error info:', info)
    
    // Return false to prevent error from propagating further
    return false
  }
}
```

## Lifecycle Diagram

```
New Vue()
    ↓
beforeCreate
    ↓
created (data, methods, computed available)
    ↓
beforeMount
    ↓
mounted (DOM available)
    ↓
... component is running ...
    ↓ (when data changes)
beforeUpdate
    ↓
updated
    ↓
... component continues running ...
    ↓ (when $destroy() is called or parent destroyed)
beforeDestroy
    ↓
destroyed
```

---

## Custom Directives

Custom directives provide a way to directly manipulate DOM elements.

### Directive Hook Functions

#### `bind`
Called only once, when the directive is first bound to the element.

#### `inserted`
Called when the bound element has been inserted into its parent node.

#### `update`
Called after the containing component's VNode has updated.

#### `componentUpdated`
Called after the containing component's VNode and the VNodes of its children have updated.

#### `unbind`
Called only once, when the directive is unbound from the element.

### Directive Hook Arguments

All hooks receive these arguments:

- **el**: The element the directive is bound to
- **binding**: An object containing:
  - `name`: Directive name, without the `v-` prefix
  - `value`: The value passed to the directive
  - `oldValue`: The previous value, only available in `update` and `componentUpdated`
  - `expression`: The expression of the binding as a string
  - `arg`: The argument passed to the directive, if any
  - `modifiers`: An object containing modifiers, if any
- **vnode**: The virtual node produced by Vue's compiler
- **oldVnode**: The previous virtual node, only available in `update` and `componentUpdated`

### Examples

#### Simple Directive

```javascript
// Global registration
Vue.directive('focus', {
  inserted(el) {
    el.focus()
  }
})

// Local registration
export default {
  directives: {
    focus: {
      inserted(el) {
        el.focus()
      }
    }
  }
}

// Usage: <input v-focus>
```

#### Directive with Value

```javascript
Vue.directive('color', {
  bind(el, binding) {
    el.style.color = binding.value
  },
  update(el, binding) {
    el.style.color = binding.value
  }
})

// Usage: <p v-color="'red'">This text is red</p>
```

#### Directive with Arguments and Modifiers

```javascript
Vue.directive('pin', {
  bind(el, binding) {
    el.style.position = 'fixed'
    const s = (binding.arg === 'left' ? 'left' : 'top')
    el.style[s] = binding.value + 'px'
  }
})

// Usage: <p v-pin:top="200">Stick me 200px from the top</p>
```

#### Advanced Directive Example

```javascript
Vue.directive('tooltip', {
  bind(el, binding, vnode) {
    // Create tooltip element
    const tooltip = document.createElement('div')
    tooltip.textContent = binding.value
    tooltip.className = 'tooltip'
    tooltip.style.cssText = `
      position: absolute;
      background: black;
      color: white;
      padding: 4px 8px;
      border-radius: 4px;
      font-size: 12px;
      z-index: 1000;
      pointer-events: none;
      opacity: 0;
      transition: opacity 0.3s;
    `
    
    // Store tooltip reference
    el._tooltip = tooltip
    document.body.appendChild(tooltip)
    
    // Add event listeners
    el.addEventListener('mouseenter', el._showTooltip = () => {
      const rect = el.getBoundingClientRect()
      tooltip.style.left = rect.left + 'px'
      tooltip.style.top = (rect.top - tooltip.offsetHeight - 5) + 'px'
      tooltip.style.opacity = '1'
    })
    
    el.addEventListener('mouseleave', el._hideTooltip = () => {
      tooltip.style.opacity = '0'
    })
  },
  
  update(el, binding) {
    el._tooltip.textContent = binding.value
  },
  
  unbind(el) {
    document.body.removeChild(el._tooltip)
    el.removeEventListener('mouseenter', el._showTooltip)
    el.removeEventListener('mouseleave', el._hideTooltip)
  }
})

// Usage: <button v-tooltip="'This is a tooltip'">Hover me</button>
```

#### Function Directive Shorthand

```javascript
// When you only need bind and update
Vue.directive('highlight', function(el, binding) {
  el.style.backgroundColor = binding.value
})

// Equivalent to:
Vue.directive('highlight', {
  bind(el, binding) {
    el.style.backgroundColor = binding.value
  },
  update(el, binding) {
    el.style.backgroundColor = binding.value
  }
})
```

---

## Functional Components

Functional components are stateless and instanceless components that are simply functions.

### Basic Functional Component

```javascript
// As a function
function MyFunctionalComponent(createElement, context) {
  return createElement('div', context.data, context.children)
}

// As an object with functional: true
export default {
  functional: true,
  render(createElement, context) {
    return createElement('div', context.data, context.children)
  }
}
```

### Render Context

The `context` object contains:

- **props**: Object of props
- **children**: Array of VNode children
- **slots**: Function returning slots
- **data**: VNodeData object
- **parent**: Reference to parent component
- **listeners**: Object of event listeners
- **injections**: Object of injections

### Examples

#### Simple Functional Component

```javascript
export default {
  functional: true,
  props: ['message'],
  render(h, { props }) {
    return h('div', `Hello ${props.message}!`)
  }
}
```

#### Functional Component with Slots

```javascript
export default {
  functional: true,
  render(h, { children, slots }) {
    return h('div', [
      h('header', slots().header),
      h('main', children),
      h('footer', slots().footer)
    ])
  }
}
```

#### Smart List Component

```javascript
const SmartList = {
  functional: true,
  props: {
    items: {
      type: Array,
      required: true
    },
    isOrdered: Boolean
  },
  render(h, { props }) {
    const tag = props.isOrdered ? 'ol' : 'ul'
    const children = props.items.map(item => h('li', item))
    return h(tag, children)
  }
}

// Usage
// <smart-list :items="['Apple', 'Orange', 'Banana']" :is-ordered="true"></smart-list>
```

#### Higher-Order Component

```javascript
function withLoading(WrappedComponent) {
  return {
    functional: true,
    props: {
      isLoading: Boolean
    },
    render(h, { props, data, children }) {
      if (props.isLoading) {
        return h('div', 'Loading...')
      }
      return h(WrappedComponent, data, children)
    }
  }
}

// Usage
const MyComponentWithLoading = withLoading(MyComponent)
```

#### Event Handling in Functional Components

```javascript
export default {
  functional: true,
  render(h, { listeners, data }) {
    return h('button', {
      ...data,
      on: {
        click: listeners.click || (() => {})
      }
    }, 'Click me')
  }
}
```

#### Passing Attributes and Events

```javascript
export default {
  functional: true,
  render(h, { data, children }) {
    return h('div', {
      ...data,
      class: {
        'my-wrapper': true,
        ...data.class
      },
      style: {
        padding: '10px',
        ...data.style
      }
    }, children)
  }
}
```

---

## Built-in Components

### `<keep-alive>`

The `<keep-alive>` component caches dynamically switched components to preserve their state.

#### Props

- **include** (String|RegExp|Array): Only components with matching names will be cached
- **exclude** (String|RegExp|Array): Components with matching names will not be cached
- **max** (Number): Maximum number of component instances to cache

#### Examples

```vue
<!-- Basic usage -->
<keep-alive>
  <component :is="currentComponent"></component>
</keep-alive>

<!-- With include/exclude -->
<keep-alive include="a,b">
  <component :is="view"></component>
</keep-alive>

<keep-alive :include="/a|b/">
  <component :is="view"></component>
</keep-alive>

<keep-alive :include="['a', 'b']">
  <component :is="view"></component>
</keep-alive>

<!-- With max cache limit -->
<keep-alive :max="10">
  <component :is="view"></component>
</keep-alive>

<!-- With router-view -->
<keep-alive>
  <router-view></router-view>
</keep-alive>
```

#### Lifecycle Hooks for Cached Components

```javascript
export default {
  activated() {
    // Called when component is activated from cache
    console.log('Component activated')
    this.refreshData()
  },
  deactivated() {
    // Called when component is deactivated and cached
    console.log('Component deactivated')
    this.pauseOperations()
  }
}
```

### `<transition>`

The `<transition>` component provides transition effects for single elements/components.

#### Props

- **name** (String): Used to automatically generate transition CSS class names
- **appear** (Boolean): Whether to apply transition on initial render
- **css** (Boolean): Whether to apply CSS transition classes
- **type** (String): Specify transition type to wait for ('transition' or 'animation')
- **mode** (String): Control timing of entering/leaving transitions ('out-in' or 'in-out')
- **duration** (Number|Object): Specify explicit transition durations
- **enter-class** (String): Custom enter class
- **leave-class** (String): Custom leave class
- **appear-class** (String): Custom appear class
- **enter-to-class** (String): Custom enter-to class
- **leave-to-class** (String): Custom leave-to class
- **appear-to-class** (String): Custom appear-to class
- **enter-active-class** (String): Custom enter-active class
- **leave-active-class** (String): Custom leave-active class
- **appear-active-class** (String): Custom appear-active class

#### Events

- **before-enter**: Before enter transition starts
- **enter**: Enter transition starts
- **after-enter**: Enter transition ends
- **enter-cancelled**: Enter transition cancelled
- **before-leave**: Before leave transition starts
- **leave**: Leave transition starts
- **after-leave**: Leave transition ends
- **leave-cancelled**: Leave transition cancelled
- **before-appear**: Before appear transition starts
- **appear**: Appear transition starts
- **after-appear**: Appear transition ends
- **appear-cancelled**: Appear transition cancelled

#### CSS Examples

```vue
<template>
  <div>
    <button @click="show = !show">Toggle</button>
    
    <!-- Basic fade transition -->
    <transition name="fade">
      <p v-if="show">Hello Vue!</p>
    </transition>
  </div>
</template>

<style>
/* Fade transition */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}

/* Slide transition */
.slide-enter-active, .slide-leave-active {
  transition: transform 0.3s;
}
.slide-enter, .slide-leave-to {
  transform: translateX(100px);
}

/* Bounce animation */
.bounce-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
</style>
```

#### JavaScript Hooks

```vue
<template>
  <transition
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:after-enter="afterEnter"
    v-on:enter-cancelled="enterCancelled"
    v-on:before-leave="beforeLeave"
    v-on:leave="leave"
    v-on:after-leave="afterLeave"
    v-on:leave-cancelled="leaveCancelled"
    v-bind:css="false"
  >
    <p v-if="show">Hello Vue!</p>
  </transition>
</template>

<script>
export default {
  methods: {
    beforeEnter(el) {
      el.style.opacity = 0
      el.style.transform = 'scale(0)'
    },
    enter(el, done) {
      el.offsetHeight // trigger reflow
      el.style.transition = 'all 0.3s'
      el.style.opacity = 1
      el.style.transform = 'scale(1)'
      setTimeout(done, 300)
    },
    afterEnter(el) {
      console.log('Enter transition completed')
    },
    beforeLeave(el) {
      el.style.transition = 'all 0.3s'
    },
    leave(el, done) {
      el.style.opacity = 0
      el.style.transform = 'scale(0)'
      setTimeout(done, 300)
    },
    afterLeave(el) {
      console.log('Leave transition completed')
    }
  }
}
</script>
```

### `<transition-group>`

The `<transition-group>` component provides transition effects for multiple elements/components.

#### Props

All props from `<transition>` plus:

- **tag** (String): Wrapper element tag (default: 'span')
- **move-class** (String): CSS class for move transitions

#### Examples

```vue
<template>
  <div>
    <button @click="add">Add</button>
    <button @click="remove">Remove</button>
    
    <transition-group name="list" tag="p">
      <span v-for="item in items" :key="item" class="list-item">
        {{ item }}
      </span>
    </transition-group>
  </div>
</template>

<style>
.list-item {
  display: inline-block;
  margin-right: 10px;
}

.list-enter-active, .list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

/* Move transition */
.list-move {
  transition: transform 1s;
}
</style>

<script>
export default {
  data() {
    return {
      items: [1, 2, 3, 4, 5],
      nextNum: 6
    }
  },
  methods: {
    randomIndex() {
      return Math.floor(Math.random() * this.items.length)
    },
    add() {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove() {
      this.items.splice(this.randomIndex(), 1)
    }
  }
}
</script>
```

---

## TypeScript Support

Vue 2.5+ provides better TypeScript support with improved type inference.

### Basic Component with TypeScript

```typescript
import Vue from 'vue'
import { Component, Prop, Watch } from 'vue-property-decorator'

@Component({
  components: {
    // Local components
  }
})
export default class MyComponent extends Vue {
  // Data
  message: string = 'Hello TypeScript!'
  count: number = 0
  
  // Props
  @Prop({ type: String, required: true })
  readonly title!: string
  
  @Prop({ type: Number, default: 10 })
  readonly max!: number
  
  // Computed
  get computedMessage(): string {
    return `${this.title}: ${this.message}`
  }
  
  // Methods
  increment(): void {
    if (this.count < this.max) {
      this.count++
    }
  }
  
  // Watchers
  @Watch('count')
  onCountChanged(newVal: number, oldVal: number): void {
    console.log(`Count changed from ${oldVal} to ${newVal}`)
  }
  
  // Lifecycle hooks
  created(): void {
    console.log('Component created')
  }
  
  mounted(): void {
    console.log('Component mounted')
  }
}
```

### Vue Instance Types

```typescript
import Vue, { VNode, CreateElement } from 'vue'

// Component options with TypeScript
interface ComponentData {
  message: string
  count: number
}

interface ComponentMethods {
  increment(): void
  reset(): void
}

interface ComponentComputed {
  doubleCount: number
}

interface ComponentProps {
  initialCount: number
}

const component = new Vue<ComponentData, ComponentMethods, ComponentComputed, ComponentProps>({
  props: {
    initialCount: Number
  },
  data(): ComponentData {
    return {
      message: 'Hello',
      count: this.initialCount || 0
    }
  },
  computed: {
    doubleCount(): number {
      return this.count * 2
    }
  },
  methods: {
    increment(): void {
      this.count++
    },
    reset(): void {
      this.count = 0
    }
  }
})
```

### Custom Type Definitions

```typescript
// types/vue-custom.d.ts
declare module 'vue/types/vue' {
  interface Vue {
    $myCustomProperty: string
    $myCustomMethod(param: string): void
  }
}

declare module 'vue/types/options' {
  interface ComponentOptions<V extends Vue> {
    myCustomOption?: string
  }
}

// Plugin type definition
declare module 'vue/types/vue' {
  interface VueConstructor {
    myGlobalMethod(): void
  }
}
```

### Render Function with TypeScript

```typescript
import Vue, { CreateElement, VNode } from 'vue'

export default Vue.extend({
  name: 'MyRenderComponent',
  props: {
    level: {
      type: Number,
      required: true
    }
  },
  render(h: CreateElement): VNode {
    return h(
      'h' + this.level,
      {
        attrs: {
          id: 'my-heading'
        },
        class: {
          'heading': true,
          'heading--large': this.level <= 2
        }
      },
      this.$slots.default
    )
  }
})
```

### Store Types (Vuex)

```typescript
import Vuex, { StoreOptions } from 'vuex'

interface RootState {
  version: string
}

interface UserState {
  name: string
  email: string
}

const userModule: Module<UserState, RootState> = {
  namespaced: true,
  state: {
    name: '',
    email: ''
  },
  mutations: {
    setName(state, name: string) {
      state.name = name
    },
    setEmail(state, email: string) {
      state.email = email
    }
  },
  actions: {
    updateUser({ commit }, user: Partial<UserState>) {
      if (user.name) commit('setName', user.name)
      if (user.email) commit('setEmail', user.email)
    }
  },
  getters: {
    fullProfile: (state): string => {
      return `${state.name} <${state.email}>`
    }
  }
}

const store: StoreOptions<RootState> = {
  state: {
    version: '1.0.0'
  },
  modules: {
    user: userModule
  }
}

export default new Vuex.Store<RootState>(store)
```

---

## Examples and Usage Patterns

### Complete Component Example

```vue
<template>
  <div class="user-profile">
    <!-- Header with user info -->
    <header class="profile-header">
      <img 
        :src="user.avatar" 
        :alt="`${user.name} avatar`"
        class="avatar"
        @error="handleImageError"
      >
      <div class="user-info">
        <h1>{{ user.name }}</h1>
        <p class="user-email">{{ user.email }}</p>
        <p class="member-since">Member since {{ formattedJoinDate }}</p>
      </div>
    </header>

    <!-- Navigation tabs -->
    <nav class="profile-nav">
      <button 
        v-for="tab in tabs"
        :key="tab.id"
        :class="['nav-tab', { active: activeTab === tab.id }]"
        @click="setActiveTab(tab.id)"
      >
        {{ tab.label }}
      </button>
    </nav>

    <!-- Tab content with transitions -->
    <transition name="fade" mode="out-in">
      <div :key="activeTab" class="tab-content">
        <!-- Posts tab -->
        <div v-if="activeTab === 'posts'" class="posts-section">
          <h2>Posts</h2>
          <div v-if="loading" class="loading">Loading posts...</div>
          <div v-else-if="posts.length === 0" class="no-posts">
            No posts yet.
          </div>
          <transition-group v-else name="list" tag="div" class="posts-list">
            <article 
              v-for="post in posts" 
              :key="post.id"
              class="post-card"
            >
              <h3>{{ post.title }}</h3>
              <p>{{ post.excerpt }}</p>
              <time :datetime="post.publishedAt">
                {{ formatDate(post.publishedAt) }}
              </time>
            </article>
          </transition-group>
        </div>

        <!-- Settings tab -->
        <div v-else-if="activeTab === 'settings'" class="settings-section">
          <h2>Settings</h2>
          <form @submit.prevent="saveSettings">
            <div class="form-group">
              <label for="display-name">Display Name</label>
              <input 
                id="display-name"
                v-model="settings.displayName"
                type="text"
                required
              >
            </div>
            
            <div class="form-group">
              <label for="bio">Bio</label>
              <textarea 
                id="bio"
                v-model="settings.bio"
                rows="4"
              ></textarea>
            </div>
            
            <div class="form-group">
              <label>
                <input 
                  v-model="settings.notifications"
                  type="checkbox"
                >
                Enable email notifications
              </label>
            </div>
            
            <button 
              type="submit" 
              :disabled="!settingsChanged"
              class="save-button"
            >
              Save Changes
            </button>
          </form>
        </div>

        <!-- Activity tab -->
        <div v-else-if="activeTab === 'activity'" class="activity-section">
          <h2>Recent Activity</h2>
          <ul class="activity-list">
            <li 
              v-for="activity in activities" 
              :key="activity.id"
              class="activity-item"
            >
              <span class="activity-type">{{ activity.type }}</span>
              <span class="activity-description">{{ activity.description }}</span>
              <time class="activity-time">{{ timeAgo(activity.timestamp) }}</time>
            </li>
          </ul>
        </div>
      </div>
    </transition>

    <!-- Custom modal component -->
    <modal 
      v-if="showModal"
      :title="modalTitle"
      @close="closeModal"
    >
      <p>{{ modalContent }}</p>
      <template #footer>
        <button @click="closeModal">Close</button>
      </template>
    </modal>
  </div>
</template>

<script>
import Modal from './Modal.vue'
import { formatDistanceToNow, format } from 'date-fns'

export default {
  name: 'UserProfile',
  
  components: {
    Modal
  },
  
  props: {
    userId: {
      type: [String, Number],
      required: true
    }
  },
  
  data() {
    return {
      user: {},
      posts: [],
      activities: [],
      activeTab: 'posts',
      loading: false,
      showModal: false,
      modalTitle: '',
      modalContent: '',
      settings: {
        displayName: '',
        bio: '',
        notifications: true
      },
      originalSettings: {},
      tabs: [
        { id: 'posts', label: 'Posts' },
        { id: 'settings', label: 'Settings' },
        { id: 'activity', label: 'Activity' }
      ]
    }
  },
  
  computed: {
    formattedJoinDate() {
      return this.user.joinedAt 
        ? format(new Date(this.user.joinedAt), 'MMMM yyyy')
        : ''
    },
    
    settingsChanged() {
      return JSON.stringify(this.settings) !== JSON.stringify(this.originalSettings)
    }
  },
  
  watch: {
    userId: {
      immediate: true,
      handler(newUserId) {
        if (newUserId) {
          this.loadUserData()
        }
      }
    },
    
    activeTab(newTab) {
      // Load tab-specific data when switching tabs
      if (newTab === 'posts' && this.posts.length === 0) {
        this.loadPosts()
      } else if (newTab === 'activity' && this.activities.length === 0) {
        this.loadActivities()
      }
    }
  },
  
  async created() {
    // Load initial data
    await this.loadUserData()
    if (this.activeTab === 'posts') {
      this.loadPosts()
    }
  },
  
  mounted() {
    // Set up event listeners
    this.$el.addEventListener('keydown', this.handleKeydown)
  },
  
  beforeDestroy() {
    // Cleanup
    this.$el.removeEventListener('keydown', this.handleKeydown)
  },
  
  methods: {
    async loadUserData() {
      try {
        const response = await fetch(`/api/users/${this.userId}`)
        this.user = await response.json()
        
        // Initialize settings with user data
        this.settings = {
          displayName: this.user.name,
          bio: this.user.bio || '',
          notifications: this.user.notifications !== false
        }
        this.originalSettings = { ...this.settings }
        
      } catch (error) {
        console.error('Failed to load user data:', error)
        this.showErrorModal('Failed to load user profile')
      }
    },
    
    async loadPosts() {
      this.loading = true
      try {
        const response = await fetch(`/api/users/${this.userId}/posts`)
        this.posts = await response.json()
      } catch (error) {
        console.error('Failed to load posts:', error)
        this.showErrorModal('Failed to load posts')
      } finally {
        this.loading = false
      }
    },
    
    async loadActivities() {
      try {
        const response = await fetch(`/api/users/${this.userId}/activities`)
        this.activities = await response.json()
      } catch (error) {
        console.error('Failed to load activities:', error)
        this.showErrorModal('Failed to load activities')
      }
    },
    
    async saveSettings() {
      try {
        const response = await fetch(`/api/users/${this.userId}/settings`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(this.settings)
        })
        
        if (response.ok) {
          this.originalSettings = { ...this.settings }
          this.$emit('settings-saved', this.settings)
          this.showSuccessModal('Settings saved successfully!')
        } else {
          throw new Error('Failed to save settings')
        }
      } catch (error) {
        console.error('Failed to save settings:', error)
        this.showErrorModal('Failed to save settings')
      }
    },
    
    setActiveTab(tabId) {
      this.activeTab = tabId
      this.$emit('tab-changed', tabId)
    },
    
    handleImageError(event) {
      // Replace with default avatar on error
      event.target.src = '/assets/default-avatar.png'
    },
    
    handleKeydown(event) {
      // Handle keyboard navigation
      if (event.key === 'Escape' && this.showModal) {
        this.closeModal()
      }
    },
    
    formatDate(dateString) {
      return format(new Date(dateString), 'MMM dd, yyyy')
    },
    
    timeAgo(timestamp) {
      return formatDistanceToNow(new Date(timestamp), { addSuffix: true })
    },
    
    showErrorModal(message) {
      this.modalTitle = 'Error'
      this.modalContent = message
      this.showModal = true
    },
    
    showSuccessModal(message) {
      this.modalTitle = 'Success'
      this.modalContent = message
      this.showModal = true
    },
    
    closeModal() {
      this.showModal = false
    }
  },
  
  // Custom filters for this component
  filters: {
    truncate(text, length = 100) {
      if (text.length <= length) return text
      return text.substring(0, length) + '...'
    }
  }
}
</script>

<style scoped>
.user-profile {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.profile-header {
  display: flex;
  align-items: center;
  margin-bottom: 30px;
  padding: 20px;
  background: #f8f9fa;
  border-radius: 8px;
}

.avatar {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  margin-right: 20px;
  object-fit: cover;
}

.user-info h1 {
  margin: 0 0 5px 0;
  color: #333;
}

.user-email {
  color: #666;
  margin: 0 0 5px 0;
}

.member-since {
  color: #888;
  font-size: 0.9em;
  margin: 0;
}

.profile-nav {
  display: flex;
  border-bottom: 2px solid #e9ecef;
  margin-bottom: 20px;
}

.nav-tab {
  background: none;
  border: none;
  padding: 12px 24px;
  cursor: pointer;
  font-size: 16px;
  color: #666;
  border-bottom: 3px solid transparent;
  transition: all 0.3s ease;
}

.nav-tab:hover {
  color: #007bff;
}

.nav-tab.active {
  color: #007bff;
  border-bottom-color: #007bff;
}

.tab-content {
  min-height: 300px;
}

.loading {
  text-align: center;
  padding: 40px;
  color: #666;
}

.no-posts {
  text-align: center;
  padding: 40px;
  color: #888;
}

.posts-list {
  display: grid;
  gap: 20px;
}

.post-card {
  padding: 20px;
  border: 1px solid #e9ecef;
  border-radius: 8px;
  transition: box-shadow 0.3s ease;
}

.post-card:hover {
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}

.post-card h3 {
  margin: 0 0 10px 0;
  color: #333;
}

.post-card p {
  color: #666;
  line-height: 1.5;
  margin: 0 0 10px 0;
}

.post-card time {
  color: #888;
  font-size: 0.9em;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: 500;
  color: #333;
}

.form-group input,
.form-group textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

.form-group input:focus,
.form-group textarea:focus {
  outline: none;
  border-color: #007bff;
  box-shadow: 0 0 0 2px rgba(0,123,255,0.25);
}

.save-button {
  background: #007bff;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background 0.3s ease;
}

.save-button:hover:not(:disabled) {
  background: #0056b3;
}

.save-button:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.activity-list {
  list-style: none;
  padding: 0;
}

.activity-item {
  display: flex;
  align-items: center;
  padding: 15px;
  border-bottom: 1px solid #e9ecef;
}

.activity-type {
  background: #e9ecef;
  color: #495057;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 0.8em;
  font-weight: 500;
  margin-right: 15px;
  min-width: 80px;
  text-align: center;
}

.activity-description {
  flex: 1;
  color: #333;
}

.activity-time {
  color: #888;
  font-size: 0.9em;
}

/* Transitions */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}

.list-enter-active, .list-leave-active {
  transition: all 0.3s ease;
}

.list-enter, .list-leave-to {
  opacity: 0;
  transform: translateY(20px);
}

.list-move {
  transition: transform 0.3s ease;
}
</style>
```

### Modal Component Example

```vue
<template>
  <transition name="modal">
    <div class="modal-overlay" @click="$emit('close')">
      <div class="modal" @click.stop>
        <header class="modal-header">
          <h3>{{ title }}</h3>
          <button class="close-button" @click="$emit('close')">×</button>
        </header>
        
        <main class="modal-body">
          <slot></slot>
        </main>
        
        <footer v-if="$slots.footer" class="modal-footer">
          <slot name="footer"></slot>
        </footer>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: 'Modal',
  
  props: {
    title: {
      type: String,
      default: 'Modal'
    }
  },
  
  mounted() {
    document.addEventListener('keydown', this.handleEscape)
    document.body.style.overflow = 'hidden'
  },
  
  beforeDestroy() {
    document.removeEventListener('keydown', this.handleEscape)
    document.body.style.overflow = ''
  },
  
  methods: {
    handleEscape(event) {
      if (event.key === 'Escape') {
        this.$emit('close')
      }
    }
  }
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal {
  background: white;
  border-radius: 8px;
  max-width: 500px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  border-bottom: 1px solid #e9ecef;
}

.modal-header h3 {
  margin: 0;
}

.close-button {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #666;
  padding: 0;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.close-button:hover {
  color: #333;
}

.modal-body {
  padding: 20px;
}

.modal-footer {
  padding: 20px;
  border-top: 1px solid #e9ecef;
  text-align: right;
}

/* Modal transitions */
.modal-enter-active, .modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-active .modal,
.modal-leave-active .modal {
  transition: transform 0.3s ease;
}

.modal-enter, .modal-leave-to {
  opacity: 0;
}

.modal-enter .modal,
.modal-leave-to .modal {
  transform: scale(0.9) translateY(-20px);
}
</style>
```

### Plugin Development Example

```javascript
// toast-plugin.js
const ToastPlugin = {
  install(Vue, options = {}) {
    // Default options
    const defaultOptions = {
      duration: 3000,
      position: 'top-right',
      maxToasts: 5
    }
    
    const config = { ...defaultOptions, ...options }
    
    // Toast component
    const ToastComponent = Vue.extend({
      template: `
        <transition name="toast" @after-leave="$emit('remove')">
          <div 
            v-if="visible"
            :class="['toast', 'toast--' + type, 'toast--' + position]"
            @click="close"
          >
            <div class="toast__icon">
              <span v-if="type === 'success'">✓</span>
              <span v-else-if="type === 'error'">✗</span>
              <span v-else-if="type === 'warning'">⚠</span>
              <span v-else>ℹ</span>
            </div>
            <div class="toast__content">
              <div v-if="title" class="toast__title">{{ title }}</div>
              <div class="toast__message">{{ message }}</div>
            </div>
            <button class="toast__close" @click="close">×</button>
          </div>
        </transition>
      `,
      props: {
        message: String,
        title: String,
        type: {
          type: String,
          default: 'info',
          validator: value => ['success', 'error', 'warning', 'info'].includes(value)
        },
        duration: {
          type: Number,
          default: config.duration
        },
        position: {
          type: String,
          default: config.position
        }
      },
      data() {
        return {
          visible: false
        }
      },
      mounted() {
        this.visible = true
        if (this.duration > 0) {
          this.timer = setTimeout(() => {
            this.close()
          }, this.duration)
        }
      },
      beforeDestroy() {
        clearTimeout(this.timer)
      },
      methods: {
        close() {
          this.visible = false
        }
      }
    })
    
    // Toast manager
    class ToastManager {
      constructor() {
        this.toasts = []
        this.container = null
      }
      
      createContainer() {
        if (this.container) return
        
        this.container = document.createElement('div')
        this.container.className = 'toast-container'
        document.body.appendChild(this.container)
      }
      
      show(options) {
        this.createContainer()
        
        // Limit number of toasts
        if (this.toasts.length >= config.maxToasts) {
          this.toasts[0].close()
        }
        
        const toast = new ToastComponent({
          propsData: options
        })
        
        toast.$on('remove', () => {
          this.remove(toast)
        })
        
        toast.$mount()
        this.container.appendChild(toast.$el)
        this.toasts.push(toast)
        
        return toast
      }
      
      remove(toast) {
        const index = this.toasts.indexOf(toast)
        if (index > -1) {
          this.toasts.splice(index, 1)
          toast.$destroy()
          if (toast.$el.parentNode) {
            toast.$el.parentNode.removeChild(toast.$el)
          }
        }
        
        // Remove container if no toasts
        if (this.toasts.length === 0 && this.container) {
          document.body.removeChild(this.container)
          this.container = null
        }
      }
      
      clear() {
        this.toasts.forEach(toast => toast.close())
      }
    }
    
    const toastManager = new ToastManager()
    
    // Instance method
    Vue.prototype.$toast = {
      show(message, options = {}) {
        return toastManager.show({ message, ...options })
      },
      success(message, options = {}) {
        return toastManager.show({ message, type: 'success', ...options })
      },
      error(message, options = {}) {
        return toastManager.show({ message, type: 'error', ...options })
      },
      warning(message, options = {}) {
        return toastManager.show({ message, type: 'warning', ...options })
      },
      info(message, options = {}) {
        return toastManager.show({ message, type: 'info', ...options })
      },
      clear() {
        toastManager.clear()
      }
    }
    
    // Global method
    Vue.toast = Vue.prototype.$toast
    
    // Add global styles
    if (!document.getElementById('toast-styles')) {
      const style = document.createElement('style')
      style.id = 'toast-styles'
      style.textContent = `
        .toast-container {
          position: fixed;
          z-index: 9999;
          pointer-events: none;
        }
        
        .toast {
          pointer-events: auto;
          display: flex;
          align-items: flex-start;
          min-width: 300px;
          max-width: 500px;
          margin-bottom: 10px;
          padding: 16px;
          background: white;
          border-radius: 8px;
          box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
          cursor: pointer;
        }
        
        .toast--top-right {
          position: fixed;
          top: 20px;
          right: 20px;
        }
        
        .toast--top-left {
          position: fixed;
          top: 20px;
          left: 20px;
        }
        
        .toast--bottom-right {
          position: fixed;
          bottom: 20px;
          right: 20px;
        }
        
        .toast--bottom-left {
          position: fixed;
          bottom: 20px;
          left: 20px;
        }
        
        .toast--success {
          border-left: 4px solid #28a745;
        }
        
        .toast--error {
          border-left: 4px solid #dc3545;
        }
        
        .toast--warning {
          border-left: 4px solid #ffc107;
        }
        
        .toast--info {
          border-left: 4px solid #17a2b8;
        }
        
        .toast__icon {
          margin-right: 12px;
          font-size: 18px;
          font-weight: bold;
        }
        
        .toast--success .toast__icon {
          color: #28a745;
        }
        
        .toast--error .toast__icon {
          color: #dc3545;
        }
        
        .toast--warning .toast__icon {
          color: #ffc107;
        }
        
        .toast--info .toast__icon {
          color: #17a2b8;
        }
        
        .toast__content {
          flex: 1;
        }
        
        .toast__title {
          font-weight: 600;
          margin-bottom: 4px;
          color: #333;
        }
        
        .toast__message {
          color: #666;
          line-height: 1.4;
        }
        
        .toast__close {
          background: none;
          border: none;
          font-size: 18px;
          color: #999;
          cursor: pointer;
          padding: 0;
          margin-left: 12px;
        }
        
        .toast__close:hover {
          color: #666;
        }
        
        .toast-enter-active {
          transition: all 0.3s ease;
        }
        
        .toast-leave-active {
          transition: all 0.3s ease;
        }
        
        .toast-enter {
          transform: translateX(100%);
          opacity: 0;
        }
        
        .toast-leave-to {
          transform: translateX(100%);
          opacity: 0;
        }
      `
      document.head.appendChild(style)
    }
  }
}

// Usage:
// Vue.use(ToastPlugin, { duration: 5000, position: 'top-left' })

export default ToastPlugin
```

### Custom Directive Examples

```javascript
// Collection of useful custom directives

// v-click-outside directive
Vue.directive('click-outside', {
  bind(el, binding, vnode) {
    el.clickOutsideEvent = function(event) {
      if (!(el === event.target || el.contains(event.target))) {
        vnode.context[binding.expression](event)
      }
    }
    document.addEventListener('click', el.clickOutsideEvent)
  },
  unbind(el) {
    document.removeEventListener('click', el.clickOutsideEvent)
  }
})

// v-scroll directive
Vue.directive('scroll', {
  inserted(el, binding) {
    const f = function(evt) {
      if (binding.value(evt, el)) {
        window.removeEventListener('scroll', f)
      }
    }
    window.addEventListener('scroll', f)
  }
})

// v-lazy directive for lazy loading images
Vue.directive('lazy', {
  bind(el, binding) {
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          el.src = binding.value
          el.classList.add('loaded')
          observer.unobserve(el)
        }
      })
    })
    
    observer.observe(el)
    el._observer = observer
  },
  unbind(el) {
    if (el._observer) {
      el._observer.disconnect()
    }
  }
})

// v-copy directive for copying text to clipboard
Vue.directive('copy', {
  bind(el, binding) {
    el.addEventListener('click', () => {
      const textToCopy = binding.value || el.textContent
      
      if (navigator.clipboard) {
        navigator.clipboard.writeText(textToCopy).then(() => {
          el.classList.add('copied')
          setTimeout(() => {
            el.classList.remove('copied')
          }, 1000)
        })
      } else {
        // Fallback for older browsers
        const textArea = document.createElement('textarea')
        textArea.value = textToCopy
        document.body.appendChild(textArea)
        textArea.select()
        document.execCommand('copy')
        document.body.removeChild(textArea)
        
        el.classList.add('copied')
        setTimeout(() => {
          el.classList.remove('copied')
        }, 1000)
      }
    })
  }
})

// v-debounce directive
Vue.directive('debounce', {
  bind(el, binding) {
    let timer
    el.addEventListener('input', () => {
      clearTimeout(timer)
      timer = setTimeout(() => {
        binding.value()
      }, binding.arg || 300)
    })
  }
})

// Usage examples:
/*
<!-- Click outside -->
<div v-click-outside="closeDropdown">
  Dropdown content
</div>

<!-- Scroll detection -->
<div v-scroll="handleScroll">
  Content
</div>

<!-- Lazy loading -->
<img v-lazy="imageUrl" alt="Lazy loaded image">

<!-- Copy to clipboard -->
<button v-copy="'Text to copy'">Copy</button>

<!-- Debounced input -->
<input v-debounce:500="search" v-model="searchTerm">
*/
```

---

This comprehensive documentation covers all the major public APIs, functions, and components in Vue.js 2.5.21. Each section includes detailed explanations, parameter descriptions, return values, and practical examples showing how to use the features in real-world scenarios.

The documentation is organized in a logical progression from basic concepts to advanced usage patterns, making it suitable for developers at all levels. The examples demonstrate best practices and common use cases you'll encounter when building Vue.js applications.