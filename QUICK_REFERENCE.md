# Vue.js 2.5.21 - Quick Reference Guide

## Vue Constructor

```javascript
// Basic instance
const vm = new Vue({
  el: '#app',
  data: { message: 'Hello Vue!' },
  methods: { greet() { console.log('Hello!') } }
})
```

## Vue Static Methods

| Method | Description | Example |
|--------|-------------|---------|
| `Vue.extend(options)` | Create subclass | `const Child = Vue.extend({ template: '<div>child</div>' })` |
| `Vue.nextTick(callback)` | Defer callback | `Vue.nextTick(() => { /* DOM updated */ })` |
| `Vue.set(obj, key, val)` | Add reactive property | `Vue.set(this.obj, 'newProp', 'value')` |
| `Vue.delete(obj, key)` | Delete reactive property | `Vue.delete(this.obj, 'prop')` |
| `Vue.directive(id, def)` | Register directive | `Vue.directive('focus', { inserted: el => el.focus() })` |
| `Vue.filter(id, fn)` | Register filter | `Vue.filter('capitalize', str => str.charAt(0).toUpperCase() + str.slice(1))` |
| `Vue.component(id, def)` | Register component | `Vue.component('my-comp', { template: '<div>Hello</div>' })` |
| `Vue.use(plugin)` | Install plugin | `Vue.use(MyPlugin, { option: 'value' })` |
| `Vue.mixin(mixin)` | Global mixin | `Vue.mixin({ created() { console.log('mixin!') } })` |

## Vue Global Config

```javascript
Vue.config.silent = true                    // Suppress logs
Vue.config.devtools = true                  // Enable devtools
Vue.config.errorHandler = (err, vm, info) => { /* handle */ }
Vue.config.ignoredElements = ['custom-element']
Vue.config.keyCodes = { enter: 13, esc: 27 }
```

## Instance Properties

| Property | Type | Description |
|----------|------|-------------|
| `vm.$data` | Object | Data object |
| `vm.$props` | Object | Current props |
| `vm.$el` | Element | Root DOM element |
| `vm.$options` | Object | Instantiation options |
| `vm.$parent` | Vue | Parent instance |
| `vm.$root` | Vue | Root instance |
| `vm.$children` | Array | Child components |
| `vm.$slots` | Object | Slot VNodes |
| `vm.$scopedSlots` | Object | Scoped slot functions |
| `vm.$refs` | Object | Template refs |
| `vm.$isServer` | Boolean | Server-side rendering |
| `vm.$attrs` | Object | Non-prop attributes |
| `vm.$listeners` | Object | Event listeners |

## Instance Methods

### Data
```javascript
vm.$watch('prop', (newVal, oldVal) => {})   // Watch data
vm.$set(obj, key, val)                      // Add reactive property
vm.$delete(obj, key)                        // Delete reactive property
```

### Events
```javascript
vm.$on('event', callback)                   // Listen to event
vm.$once('event', callback)                 // Listen once
vm.$off('event', callback)                  // Remove listener
vm.$emit('event', ...args)                  // Emit event
```

### Lifecycle
```javascript
vm.$mount('#app')                           // Mount instance
vm.$forceUpdate()                           // Force re-render
vm.$nextTick(() => {})                      // Next DOM update
vm.$destroy()                               // Destroy instance
```

## Component Options

### Data Options
```javascript
export default {
  data() { return { count: 0 } },
  props: ['message', 'value'],
  computed: {
    doubled() { return this.count * 2 },
    fullName: {
      get() { return `${this.first} ${this.last}` },
      set(value) { [this.first, this.last] = value.split(' ') }
    }
  },
  methods: { increment() { this.count++ } },
  watch: {
    count(newVal, oldVal) { console.log(`Count: ${oldVal} → ${newVal}`) }
  }
}
```

### DOM Options
```javascript
export default {
  template: '<div>{{ message }}</div>',
  render(h) {
    return h('div', this.message)
  }
}
```

### Asset Options
```javascript
export default {
  components: { ChildComponent },
  directives: { focus: { inserted: el => el.focus() } },
  filters: { capitalize: str => str.charAt(0).toUpperCase() + str.slice(1) }
}
```

## Lifecycle Hooks

```javascript
export default {
  beforeCreate() { /* before data/events */ },
  created() { /* data ready, DOM not ready */ },
  beforeMount() { /* before DOM render */ },
  mounted() { /* DOM ready */ },
  beforeUpdate() { /* before re-render */ },
  updated() { /* after re-render */ },
  beforeDestroy() { /* cleanup */ },
  destroyed() { /* fully destroyed */ },
  
  // Keep-alive only
  activated() { /* component activated */ },
  deactivated() { /* component deactivated */ },
  
  // Error handling
  errorCaptured(err, vm, info) { /* handle child errors */ }
}
```

## Template Syntax

### Interpolation
```html
<!-- Text -->
{{ message }}
{{ message | capitalize }}

<!-- Raw HTML -->
<div v-html="rawHtml"></div>

<!-- Attributes -->
<div v-bind:id="dynamicId"></div>
<div :id="dynamicId"></div>
<div :disabled="isDisabled"></div>

<!-- JavaScript expressions -->
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
```

### Directives
```html
<!-- Event handling -->
<button v-on:click="doThis"></button>
<button @click="doThis"></button>
<button @click="doThat('hello', $event)"></button>

<!-- Form input bindings -->
<input v-model="message">
<input v-model.lazy="message">
<input v-model.number="age">
<input v-model.trim="message">

<!-- Conditional rendering -->
<p v-if="seen">Now you see me</p>
<p v-else-if="other">Or me</p>
<p v-else>Or me</p>
<p v-show="ok">Toggle visibility</p>

<!-- List rendering -->
<li v-for="item in items" :key="item.id">{{ item.message }}</li>
<li v-for="(item, index) in items" :key="index">{{ item.message }}</li>
<li v-for="(value, key) in object" :key="key">{{ key }}: {{ value }}</li>

<!-- Class and style bindings -->
<div :class="{ active: isActive }"></div>
<div :class="[activeClass, errorClass]"></div>
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
<div :style="[baseStyles, overridingStyles]"></div>
```

### Modifiers
```html
<!-- Event modifiers -->
<form @submit.prevent="onSubmit"></form>
<a @click.stop.prevent="doThis"></a>
<input @keyup.enter="submit">
<input @keyup.page-down="onPageDown">

<!-- v-model modifiers -->
<input v-model.lazy="msg">
<input v-model.number="age">
<input v-model.trim="msg">
```

## Built-in Components

### keep-alive
```html
<keep-alive>
  <component :is="view"></component>
</keep-alive>

<keep-alive include="a,b" :max="10">
  <component :is="view"></component>
</keep-alive>
```

### transition
```html
<transition name="fade">
  <p v-if="show">hello</p>
</transition>

<transition name="custom" 
  enter-active-class="animated fadeIn"
  leave-active-class="animated fadeOut">
  <p v-if="show">hello</p>
</transition>
```

### transition-group
```html
<transition-group name="list" tag="ul">
  <li v-for="item in items" :key="item">{{ item }}</li>
</transition-group>
```

## Custom Directives

### Basic Directive
```javascript
// Global
Vue.directive('focus', {
  inserted(el) { el.focus() }
})

// Local
export default {
  directives: {
    focus: { inserted(el) { el.focus() } }
  }
}
```

### Directive Hooks
```javascript
Vue.directive('my-directive', {
  bind(el, binding, vnode) { /* once on bind */ },
  inserted(el, binding, vnode) { /* when inserted */ },
  update(el, binding, vnode, oldVnode) { /* on update */ },
  componentUpdated(el, binding, vnode, oldVnode) { /* after component update */ },
  unbind(el, binding, vnode) { /* once on unbind */ }
})
```

### Directive Arguments
```javascript
// binding object contains:
{
  name: 'my-directive',     // directive name
  value: 'foo',             // binding value
  oldValue: 'bar',          // previous value
  expression: 'message',    // expression string
  arg: 'foo',              // argument
  modifiers: { foo: true }  // modifiers object
}
```

## Functional Components

```javascript
export default {
  functional: true,
  props: ['message'],
  render(h, { props, children, data, parent, listeners, injections }) {
    return h('div', props.message)
  }
}

// Or as a function
function MyFunctionalComponent(h, { props, children, data }) {
  return h('div', props.message)
}
```

## Common Patterns

### Parent-Child Communication
```javascript
// Parent to child: props
<child-component :message="parentMessage"></child-component>

// Child to parent: events
// Child
this.$emit('update', newValue)
// Parent
<child-component @update="handleUpdate"></child-component>

// Two-way binding
// Child
this.$emit('input', newValue)
// Parent
<child-component v-model="value"></child-component>
```

### Provide/Inject
```javascript
// Parent
export default {
  provide: {
    theme: 'dark'
  }
}

// Child (any level)
export default {
  inject: ['theme']
}
```

### Mixins
```javascript
const myMixin = {
  created() { console.log('mixin hook called') },
  methods: { hello() { console.log('hello from mixin!') } }
}

export default {
  mixins: [myMixin],
  created() { console.log('component hook called') }
}
```

### Slots
```html
<!-- Parent -->
<child-component>
  <template #header>Header content</template>
  <template #default>Default content</template>
  <template #footer="{ user }">Footer for {{ user.name }}</template>
</child-component>

<!-- Child -->
<div>
  <header><slot name="header"></slot></header>
  <main><slot></slot></main>
  <footer><slot name="footer" :user="user"></slot></footer>
</div>
```

### Dynamic Components
```html
<component :is="currentComponent" v-bind="componentProps"></component>

<keep-alive>
  <component :is="currentComponent"></component>
</keep-alive>
```

## Event Bus Pattern
```javascript
// Create event bus
const EventBus = new Vue()

// Emit events
EventBus.$emit('message', 'Hello!')

// Listen to events
EventBus.$on('message', (message) => {
  console.log(message)
})

// Clean up
EventBus.$off('message')
```

## Plugin Development
```javascript
const MyPlugin = {
  install(Vue, options) {
    // Add global method
    Vue.myGlobalMethod = () => {}
    
    // Add instance method
    Vue.prototype.$myMethod = () => {}
    
    // Add global directive
    Vue.directive('my-directive', {})
    
    // Add component
    Vue.component('my-component', {})
    
    // Add mixin
    Vue.mixin({})
  }
}

Vue.use(MyPlugin, { option: 'value' })
```

## Common Gotchas

### Reactivity
```javascript
// ❌ Won't be reactive
this.items[0] = newValue
this.items.length = 0
this.object.newProp = 'value'

// ✅ Reactive
Vue.set(this.items, 0, newValue)
this.$set(this.items, 0, newValue)
this.items.splice(0, 1, newValue)
this.items.splice(0)
Vue.set(this.object, 'newProp', 'value')
this.$set(this.object, 'newProp', 'value')
```

### Keys in Lists
```html
<!-- ❌ No key -->
<div v-for="item in items">{{ item.message }}</div>

<!-- ✅ With unique key -->
<div v-for="item in items" :key="item.id">{{ item.message }}</div>
```

### Avoid Mutating Props
```javascript
// ❌ Don't mutate props directly
export default {
  props: ['value'],
  methods: {
    updateValue() {
      this.value = 'new value' // ❌ Don't do this
    }
  }
}

// ✅ Use local data or emit events
export default {
  props: ['value'],
  data() {
    return {
      localValue: this.value
    }
  },
  methods: {
    updateValue() {
      this.$emit('input', 'new value') // ✅ Emit event
    }
  }
}
```

---

## Performance Tips

1. **Use `v-show` vs `v-if`**: `v-show` for frequent toggles, `v-if` for conditional rendering
2. **Use `Object.freeze()`** for large lists that won't change
3. **Use `functional` components** for stateless components
4. **Use `keep-alive`** to cache dynamic components
5. **Use `v-once`** for expensive one-time rendering
6. **Lazy load components** with dynamic imports
7. **Use `key` attribute** for efficient list updates

## Debugging Tips

```javascript
// Vue devtools
Vue.config.devtools = true

// Performance tracking
Vue.config.performance = true

// Warnings in development
Vue.config.silent = false

// Error handling
Vue.config.errorHandler = (err, vm, info) => {
  console.error('Vue error:', err, info)
}

// Debug components
export default {
  name: 'MyComponent', // Shows in devtools
  created() {
    console.log('Component created:', this.$options.name)
  }
}
```