# Vue 2.x Public API Reference

> **Version covered:** 2.5.x  
> All APIs listed here are **stable** and part of Vue’s public surface. Everything else inside `src/` is considered internal and may change without notice.

---

## Quick-Start Example

```html
<script src="https://unpkg.com/vue@2"></script>
<div id="app">
  <h1>{{ msg }}</h1>
  <child-component @update="msg = $event" />
</div>
<script>
Vue.component('child-component', {
  template: `<button @click="$emit('update', 'Hi Vue!')">Click me</button>`
})

new Vue({
  el: '#app',
  data () {
    return { msg: 'Hello World' }
  }
})
</script>
```

---

## Global APIs (`import Vue from 'vue'`)

| API | Description | Example |
|-----|-------------|---------|
| **`Vue.extend(options)`** | Creates a subclass—the basis of single-file components. | `const Sub = Vue.extend({ props:['msg'], template:'<p>{{msg}}</p>' })` |
| **`Vue.component(name, [definition])`** | Register or retrieve a global component. | `Vue.component('my-button', {...})` |
| **`Vue.directive(name, [definition])`** | Register or retrieve a global directive. | `Vue.directive('focus', { inserted: el => el.focus() })` |
| **`Vue.filter(name, [function])`** | Register or retrieve a global filter. | `Vue.filter('capitalize', str => str.charAt(0).toUpperCase()+str.slice(1))` |
| **`Vue.mixin(mixin)`** | Inject reusable options into every component. | `Vue.mixin({ created(){ console.log('hi') }})` |
| **`Vue.use(plugin, [options])`** | Install a plugin. The plugin should expose an `install` method or be a function. | `Vue.use(VueRouter)` |
| **`Vue.set(target, key, value)`** | Add a **reactive** property to an object/array. | `Vue.set(vm.user, 'age', 42)` |
| **`Vue.delete(target, key)`** | Delete a property and trigger updates. | `Vue.delete(vm.items, 3)` |
| **`Vue.nextTick([callback, context])`** | Defer a callback until after the next DOM update cycle. Returns a Promise if callback is omitted. | `await Vue.nextTick()` |
| **`Vue.observable(obj)`** | Make an object reactive outside of a component instance. | `const state = Vue.observable({ count: 0 })` |

### SSR & Tooling APIs

* **`Vue.compile(template)`** – (Full build only) returns render functions.
* **`Vue.version`** – The version string, e.g. `"2.5.21"`.

---

## The Root Constructor – `new Vue(options)`

```js
const vm = new Vue({
  el: '#app',
  data: { msg: 'Hello' },
  template: '<p>{{ msg }}</p>'
})
```

Key constructor options:

* `data`, `props`, `computed`, `methods`, `watch` – Define reactive state & observers.
* Lifecycle hooks: `beforeCreate`, `created`, `beforeMount`, `mounted`, `beforeUpdate`, `updated`, `beforeDestroy`, `destroyed`.
* `render` / `template` – Provide Virtual-DOM output.
* `components`, `directives`, `filters` – Register assets locally.

See detailed option reference below.

---

## Instance Properties (`vm.*`)

| Property | Type | Description |
|----------|------|-------------|
| `$data` | Object | Reactive component state. |
| `$el` | HTMLElement | Component’s root element (after mount). |
| `$props` | Object | Resolved prop values (read-only). |
| `$slots`, `$scopedSlots` | Object | Render children passed via slots. |
| `$refs` | Object | Registered refs. |
| `$parent` / `$root` | Vue | Immediate parent / root instance. |
| `$isServer` | Boolean | True when rendering in Node.js. |
| `$options` | Object | Merged constructor options. |
| `$watch(expOrFn, cb, [opts])` | Function | Imperative watcher, returns unwatch function. |

---

## Instance Methods

| Method | Purpose | Example |
|--------|---------|---------|
| `$set(target, key, value)` | Same as `Vue.set` but scoped to instance. | `this.$set(this.list, 4, 'x')` |
| `$delete(target, key)` | Delete a key reactively. | `this.$delete(this.user, 'temp')` |
| `$emit(event, [...args])` | Trigger a custom event. | `this.$emit('submit', payload)` |
| `$on(event, fn)` / `$once` / `$off` | Listen to component events. | `this.$on('ready', cb)` |
| `$forceUpdate()` | Bypass should-update optimisation. |
| `$nextTick([cb])` | Await DOM update. Returns Promise if no cb. | `await this.$nextTick()` |

---

## Component System

### Template Syntax Highlights

* **Interpolation** – `{{ expression }}`
* **Directives** – prefixed with `v-` e.g. `v-if`, `v-for`, `v-model`, `v-bind`, `v-on`.
* **Modifiers** – e.g. `v-on:keyup.enter`, `v-on:click.prevent`.
* **Shorthands** – `:` for `v-bind`, `@` for `v-on`, `#` for `v-slot`.

### Single-File Components (SFC)

```vue
<template>
  <button @click="increment">Clicked {{ count }} times</button>
</template>

<script>
export default {
  data: () => ({ count: 0 }),
  methods: {
    increment () { this.count++ }
  }
}
</script>

<style scoped>
button { font-size: 1.2em; }
</style>
```

---

## Built-In Components

| Component | Use-Case |
|-----------|----------|
| `<transition>` | Apply CSS/JS hooks for entering & leaving. |
| `<transition-group>` | Animate lists. |
| `<keep-alive>` | Cache inactive component instances. |
| `<slot>` | Render slot content in templates. |
| `<component :is="expr">` | Dynamic component / async component placeholder. |

Examples:

```html
<transition name="fade">
  <p v-if="ok">hello</p>
</transition>
```

```html
<keep-alive>
  <router-view />
</keep-alive>
```

---

## Custom Directives

```js
Vue.directive('focus', {
  inserted (el) { el.focus() }
})
```

Usage:

```html
<input v-focus>
```

---

## Filters _(Deprecated in Vue 3)_

```js
Vue.filter('currency', val => `$${ Number(val).toFixed(2) }`)
```

```html
<span>{{ price | currency }}</span>
```

---

## Reusable Mixins & Plugins

```js
// mixin.js
export const authMixin = {
  computed: {
    isLoggedIn () {
      return this.$store.state.user != null
    }
  }
}
```

```js
// plugin.js
export default {
  install (Vue, options) {
    Vue.prototype.$log = (...args) => console.log('[log]', ...args)
  }
}
```

```js
import Logger from './plugin'
Vue.use(Logger)
```

---

## Reactivity Caveats & Utilities

1. **Adding new reactive keys** – Use `Vue.set` / `this.$set`.
2. **Replacing arrays** – `this.list.splice(index, 1, newVal)` keeps reactivity.
3. **Immutable patterns** – You can still use `Object.freeze()` for props.

---

## Server Side Rendering (SSR)

Use `vue-server-renderer` package:

```js
const renderer = require('vue-server-renderer').createRenderer()
const app = new Vue({ template: '<div>Hello</div>' })
renderer.renderToString(app).then(html => console.log(html))
```

---

## TypeScript Support

Type definitions are shipped in `types/`. Use:

```ts
import Vue, { VNode } from 'vue'
```

For class-style components, install `vue-class-component` and `vue-property-decorator`.

---

## Versioning & Browser Support

Vue 2 supports IE 9+ (with polyfills) and all evergreen browsers.

---

## Further Reading

* **Official Guide:** https://v2.vuejs.org/guide/
* **Style Guide:** https://v2.vuejs.org/style-guide/
* **Cookbook:** https://v2.vuejs.org/cookbook/
* **FAQ & Troubleshooting:** https://v2.vuejs.org/faq/

---

© 2024 Vue.js (MIT). This documentation file is generated for internal reference. For the complete, interactive guide visit the links above.