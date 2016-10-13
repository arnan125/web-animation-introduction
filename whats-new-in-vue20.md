## Templates

### 片断实例 <sup>deprecated</sup>

每一个组件都应仅有一个根节点元素，片段实例不再被允许。如果有如下代码片段

``` html
<p>foo</p>
<p>bar</p>
```

建议直接给它们整个内容添加一个包裹元素，如下所示

``` html
<div>
  <p>foo</p>
  <p>bar</p>
</div>
```

## 生命周期

### `beforeCompile` <sup>deprecated</sup>

使用`created`钩子代替.

### `compiled` <sup>deprecated</sup>

使用`mounted`钩子代替

### `attached` <sup>deprecated</sup>

不再使用`attached`钩子，而是在其他钩子中做in-dom检测（不好翻译，意思大概是检测dom元素是否渲染完成），将

``` js
attached: function () {
    doSomething()
}
```

替换为

``` js
mounted: function () {
  this.$nextTick(function () {
    doSomething()
  })
}
```

### `detached` <sup>deprecated</sup>

不再使用`detached`钩子，而是在其他钩子中做in-dom检测，将

``` js
detached: function () {
  doSomething()
}
```

替换为

``` js
destroyed: function () {
  this.$nextTick(function () {
    doSomething()
  })
}
```

### `init` <sup>deprecated</sup>

使用`beforeCreate`代替，本质上，`init`和`beforeCreate`是一回事。为保持生命周期命名的一致性，`init`钩子被重命名为`beforeCreate`

### `ready` <sup>deprecated</sup>
使用新的`mounted`钩子代替，注意，`mounted`钩子不保证组件元素已经渲染到文档中，因此，也需要调用`Vue.nextTick`/`vm.$nextTick`。如下所示

``` js
mounted: function () {
  this.$nextTick(function () {
    // code that assumes this.$el is in-document
  })
}
```

## `v-for`

### `v-for` 应用于数组时的参数顺序

当需要使用数组索引时，过去的参数是`(index, value)`，现在则是`(value, index)`，它与js原生数组方法的参数形式保持一致，比如`forEach`、`map`等

### `v-for` 应用于对象时的参数顺序

当需要使用对象的键（`key`）时，过去的参数是`(key, value)`，现在则是`(value, key)`，它与一些常用的对象迭代器参数形式保持一致，比如[lodash](http://lodashjs.com/docs/#_forinobject-iteratee_identity-thisarg)

### `$index` and `$key` <sup>deprecated</sup>

隐式参量`$index`和`$key`已被废弃，应在`v-for`表达式中显示定义，这样可以使代码易于阅读（尤其对于vue新手），并且使嵌套循环代码的行为更清晰。

### `track-by` <sup>deprecated</sup>

属性`track-by`被`key`代替，`key`更像其他普通属性，没有`v-bind:`或者`:`前缀时被当做是一个字符串值字面量。大多数情况下，你想要动态绑定`key`的话，请使用`v-bind:`或者`:`前缀，如下所示

``` html
<div v-for="item in items" track-by="id">
```

替换为

``` html
<div v-for="item in items" v-bind:key="item.id">
```

### `v-for` 应用于数值范围

先前`v-for="number in 10"`将会遍历0到9，现在则是从1到10。

## Props

### Prop验证选项`coerce`<sup>deprecated</sup>

如果想要转换一个`prop`，请设置一个基于此`prop`的计算属性，如下所示

``` js
props: {
  username: {
    type: String,
    coerce: function (value) {
      return value
      .toLowerCase()
      .replace(/\s+/, '-')
    }
  }
}
```

替换为

``` js
props: {
 username: String,
},
computed: {
  normalizedUsername: function () {
    return this.username
    .toLowerCase()
    .replace(/\s+/, '-')
  }
}
```

几点好处如下：
- 仍然可以获得`prop`的原始值
- 显式定义转换后的`prop`名称，使得它和传入的`prop`有所区别

### Prop`twoWay`选项<sup>deprecated</sup>

Props总是单向传递下来的。为了对父组件的作用域产生影响，组件应该显式地触发一个事件而非隐式的双向绑定数据，更多信息请查看

- [Custom component events](components.html#Custom-Events)
- [Custom input components](components.html#Form-Input-Components-using-Custom-Events) (using component events)
- [Global state management](state-management.html)

### 指令`v-bind`的修饰符`.once` and `.sync` Modifiers <sup>deprecated</sup>

Props are now always one-way down. To produce side effects in the parent scope, a component needs to explicitly emit an event instead of relying on implicit binding. For more information, see:

- [Custom component events](components.html#Custom-Events)

- [Custom input components](components.html#Form-Input-Components-using-Custom-Events) (using component events)

- [Global state management](state-management.html)

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the <code>.once</code> and <code>.sync</code> modifiers.</p>

</div>

### Prop Mutation <sup>deprecated</sup>

Mutating a prop locally is now considered an anti-pattern, e.g. declaring a prop and then setting `this.myProp = 'someOtherValue'` in the component. Due to the new rendering mechanism, whenever the parent component re-renders, the child component's local changes will be overwritten.

Most use cases of mutating a prop can be replaced by one of these options:

- a data property, with the prop used to set its default value

- a computed property

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run your end-to-end test suite or app after upgrading and look for <strong>console warnings</strong> about prop mutations.</p>

</div>

### Props on a Root Instance <sup>deprecated</sup>

On root Vue instances (i.e. instances created with `new Vue({ ... })`), you must use `propsData` instead of `props`.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run your end-to-end test suite, if you have one. The <strong>failed tests</strong> should alert to you to the fact that props passed to root instances are no longer working.</p>

</div>

## Built-In Directives

### Truthiness/Falsiness with `v-bind`

When used with `v-bind`, the only falsy values are now: `null`, `undefined`, and `false`. This means `0` and empty strings will render as truthy. So for example, `v-bind:draggable="''"` will render as `draggable="true"`.

For enumerated attributes, in addition to the falsy values above, the string `"false"` will also render as `attr="false"`.

<p class="tip">Note that for other directives (e.g. `v-if` and `v-show`), JavaScript's normal truthiness still applies.</p>

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run your end-to-end test suite, if you have one. The <strong>failed tests</strong> should alert to you to any parts of your app that may be affected by this change.</p>

</div>

### Listening for Native Events on Components with `v-on`

When used on a component, `v-on` now only listens to custom events `$emit`ted by that component. To listen for a native DOM event on the root element, you can use the `.native` modifier. For example:

``` html

<my-component v-on:click.native="doSomething"></my-component>

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run your end-to-end test suite, if you have one. The <strong>failed tests</strong> should alert to you to any parts of your app that may be affected by this change.</p>

</div>

### `v-model` with `debounce` <sup>deprecated</sup>

Debouncing is used to limit how often we execute Ajax requests and other expensive operations. Vue's `debounce` attribute parameter for `v-model` made this easy for very simple cases, but it actually debounced __state updates__ rather than the expensive operations themselves. It's a subtle difference, but it comes with limitations as an application grows.

These limitations become apparent when designing a search indicator, like this one for example:

<script src="https://cdn.jsdelivr.net/lodash/4.13.1/lodash.js"></script>

<div id="debounce-search-demo" class="demo">

 <input v-model="searchQuery" placeholder="Type something">

 <strong>{{ searchIndicator }}</strong>

</div>

<script>

new Vue({

 el: '#debounce-search-demo',

 data: {

 searchQuery: '',

 searchQueryIsDirty: false,

 isCalculating: false

 },

 computed: {

 searchIndicator: function () {

 if (this.isCalculating) {

 return '⟳ Fetching new results'

 } else if (this.searchQueryIsDirty) {

 return '... Typing'

 } else {

 return '✓ Done'

 }

 }

 },

 watch: {

 searchQuery: function () {

 this.searchQueryIsDirty = true

 this.expensiveOperation()

 }

 },

 methods: {

 expensiveOperation: _.debounce(function () {

 this.isCalculating = true

 setTimeout(function () {

 this.isCalculating = false

 this.searchQueryIsDirty = false

 }.bind(this), 1000)

 }, 500)

 }

})

</script>

Using the `debounce` attribute, there'd be no way to detect the "Typing" state, because we lose access to the input's real-time state. By decoupling the debounce function from Vue however, we're able to debounce only the operation we want to limit, removing the limits on features we can develop:

``` html

<!--

By using the debounce function from lodash or another dedicated

utility library, we know the specific debounce implementation we

use will be best-in-class - and we can use it ANYWHERE. Not just

in our template.

-->

<script src="https://cdn.jsdelivr.net/lodash/4.13.1/lodash.js"></script>

<div id="debounce-search-demo">

 <input v-model="searchQuery" placeholder="Type something">

 <strong>{{ searchIndicator }}</strong>

</div>

```

``` js

new Vue({

 el: '#debounce-search-demo',

 data: {

 searchQuery: '',

 searchQueryIsDirty: false,

 isCalculating: false

 },

 computed: {

 searchIndicator: function () {

 if (this.isCalculating) {

 return '⟳ Fetching new results'

 } else if (this.searchQueryIsDirty) {

 return '... Typing'

 } else {

 return '✓ Done'

 }

 }

 },

 watch: {

 searchQuery: function () {

 this.searchQueryIsDirty = true

 this.expensiveOperation()

 }

 },

 methods: {

 // This is where the debounce actually belongs.

 expensiveOperation: _.debounce(function () {

 this.isCalculating = true

 setTimeout(function () {

 this.isCalculating = false

 this.searchQueryIsDirty = false

 }.bind(this), 1000)

 }, 500)

 }

})

```

Another advantage of this approach is there will be times when debouncing isn't quite the right wrapper function. For example, when hitting an API for search suggestions, waiting to offer suggestions until after the user has stopped typing for a period of time isn't an ideal experience. What you probably want instead is a __throttling__ function. Now since you're already using a utility library like lodash, refactoring to use its `throttle` function instead takes only a few seconds.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the <code>debounce</code> attribute.</p>

</div>

### `v-model` with `lazy` or `number` Param Attributes <sup>deprecated</sup>

The `lazy` and `number` param attributes are now modifiers, to make it more clear what That means instead of:

``` html

<input v-model="name" lazy>

<input v-model="age" type="number" number>

```

You would use:

``` html

<input v-model.lazy="name">

<input v-model.number="age" type="number">

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the these deprecated param attributes.</p>

</div>

### `v-model` with Inline `value` <sup>deprecated</sup>

`v-model` no longer cares about the initial value of an inline `value` attribute. For predictability, it will instead always treat the Vue instance data as the source of truth.

That means this element:

``` html

<input v-model="text" value="foo">

```

backed by this data:

``` js

data: {

 text: 'bar'

}

```

will render with a value of "bar" instead of "foo". The same goes for a `<textarea>` with existing content. Instead of:

``` html

<textarea v-model="text">

 hello world

</textarea>

```

You should ensure your initial value for `text` is "hello world".

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run your end-to-end test suite or app after upgrading and look for <strong>console warnings</strong> about inline value attributes with <code>v-model</code>.</p>

</div>

### `v-model` with `v-for` Iterated Primitive Values <sup>deprecated</sup>

Cases like this no longer work:

``` html

<input v-for="str in strings" v-model="str">

```

The reason is this is the equivalent JavaScript that the `<input>` would compile to:

``` js

strings.map(function (str) {

 return createElement('input', ...)

})

```

As you can see, `v-model`'s two-way binding doesn't make sense here. Setting `str` to another value in the iterator function will do nothing because it's just a local variable in the function scope.

Instead, you should use an array of __objects__ so that `v-model` can update the field on the object. For example:

``` html

<input v-for="obj in objects" v-model="obj.str">

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run your test suite, if you have one. The <strong>failed tests</strong> should alert to you to any parts of your app that may be affected by this change.</p>

</div>

### `v-bind:style` with Object Syntax and `!important` <sup>deprecated</sup>

This will no longer work:

``` html

<p v-bind:style="{ color: myColor + ' !important' }">hello</p>

```

If you really need to override another `!important`, you must use the string syntax:

``` html

<p v-bind:style="'color: ' + myColor + ' !important'">hello</p>

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of style bindings with <code>!important</code> in objects.</p>

</div>

### `v-el` and `v-ref` <sup>deprecated</sup>

For simplicity, `v-el` and `v-ref` have been merged into the `ref` attribute, accessible on a component instance via `$refs`. That means `v-el:my-element` would become `ref="myElement"` and `v-ref:my-component` would become `ref="myComponent"`. When used on a normal element, the `ref` will be the DOM element, and when used on a component, the `ref` will be the component instance.

Since `v-ref` is no longer a directive, but a special attribute, it can also be dynamically defined. This is especially useful in combination with `v-for`. For example:

``` html

<p v-for="item in items" v-bind:ref="'item' + item.id"></p>

```

Previously, `v-el`/`v-ref` combined with `v-for` would produce an array of elements/components, because there was no way to give each item a unique name. You can still achieve this behavior by given each item the same `ref`:

``` html

<p v-for="item in items" ref="items"></p>

```

Unlike in 1.x, these `$refs` are not reactive, because they're registered/updated during the render process itself. Making them reactive would require duplicate renders for every change.

On the other hand, `$refs` are designed primarily for programmatic access in JavaScript - it is not recommended to rely on them in templates, because that would mean referring to state that does not belong to the instance itself. This would violate Vue's data-driven view model.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>v-el</code> and <code>v-ref</code>.</p>

</div>

### `v-else` with `v-show` <sup>deprecated</sup>

`v-else` no longer works with `v-show`. Use `v-if` with a negation expression instead. For example, instead of:

``` html

<p v-if="foo">Foo</p>

<p v-else v-show="bar">Not foo, but bar</p>

```

You can use:

``` html

<p v-if="foo">Foo</p>

<p v-if="!foo && bar">Not foo, but bar</p>

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the <code>v-else</code> with <code>v-show</code>.</p>

</div>

## Custom Directives

Directives have a greatly reduced scope of responsibility: they are now only used for applying low-level direct DOM manipulations. In most cases, you should prefer using components as the main code-reuse abstraction.

Some of the most notable differences include:

- Directives no longer have instances. This means there's no more `this` inside directive hooks. Instead, they receive everything they might need as arguments. If you really must persist state across hooks, you can do so on `el`.

- Options such as `acceptStatement`, `deep`, `priority`, etc are all deprecated. To replace `twoWay` directives, see [this example](#Two-Way-Filters-deprecated).

- Some of the current hooks have different behavior and there are also a couple new hooks.

Fortunately, since the new directives are much simpler, you can master them more easily. Read the new [Custom Directives guide](custom-directive.html) to learn more.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of defined directives. The helper will flag all of them, as it's likely in most cases that you'll want to refactor to a component.</p>

</div>

### Directive `.literal` Modifier <sup>deprecated</sup>

The `.literal` modifier has been removed, as the same can be easily achieved by just providing a string literal as the value.

For example, you can update:

``` js

<p v-my-directive.literal="foo bar baz"></p>

```

to just:

``` html

<p v-my-directive="'foo bar baz'"></p>

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the `.literal` modifier on a directive.</p>

</div>

## Transitions

### `transition` Attribute <sup>deprecated</sup>

Vue's transition system has changed quite drastically and now uses `<transition>` and `<transition-group>` wrapper elements, rather than the `transition` attribute. It's recommended to read the new [Transitions guide](transitions.html) to learn more.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the <code>transition</code> attribute.</p>

</div>

### `Vue.transition` for Reusable Transitions <sup>deprecated</sup>

With the new transition system, you can now just [use components for reusable transitions](http://rc.vuejs.org/guide/transitions.html#Reusable-Transitions).

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.transition</code>.</p>

</div>

### Transition `stagger` Attribute <sup>deprecated</sup>

If you need to stagger list transitions, you can control timing by setting and accessing a `data-index` (or similar attribute) on an element. See [an example here](transitions.html#Staggering-List-Transitions).

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the <code>transition</code> attribute. During your update, you can transition (pun very much intended) to the new staggering strategy as well.</p>

</div>

## Events

### `events` option <sup>deprecated</sup>

The `events` option has been deprecated. Event handlers should now be registered in the `created` hook instead. Check out the [`$dispatch` and `$broadcast` migration guide](#dispatch-and-broadcast-deprecated) for a detailed example.

### `Vue.directive('on').keyCodes` <sup>deprecated</sup>

The new, more concise way to configure `keyCodes` is through`Vue.config.keyCodes`. For example:

``` js

// enable v-on:keyup.f1

Vue.config.keyCodes.f1 = 112

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the the old <code>keyCode</code> configuration syntax.</p>

</div>

### `$dispatch` and `$broadcast` <sup>deprecated</sup>

`$dispatch` and `$broadcast` are being deprecated in favor of more explicitly cross-component communication and more maintainable state management solutions, such as [Vuex](https://github.com/vuejs/vuex).

The problem is event flows that depend on a component's tree structure can be hard to reason about and very brittle when the tree becomes large. It simply doesn't scale well and we don't want to set you up for pain later. `$dispatch` and `$broadcast` also do not solve communication between sibling components.

One of the most common uses for these methods is to communicate between a parent and its direct children. In these cases, you can actually [listen to an `$emit` from a child with `v-on`](http://vuejs.org/guide/components.html#Form-Input-Components-using-Custom-Events). This allows you to keep the convenience of events with added explicitness.

However, when communicating between distant descendants/ancestors, `$emit` won't help you. Instead, the simplest possible upgrade would be to use a centralized event hub. This has the added benefit of allowing you to communicate between components no matter where they are in the component tree - even between siblings! Because Vue instances implement an event emitter interface, you can actually use an empty Vue instance for this purpose.

For example, let's say we have a todo app structured like this:

```

Todos

|-- NewTodoInput

|-- Todo

 |-- DeleteTodoButton

```

We could manage communication between components with this single event hub:

``` js

// This is the event hub we'll use in every

// component to communicate between them.

var eventHub = new Vue()

```

Then in our components, we can use `$emit`, `$on`, `$off` to emit events, listen for events, and clean up event listeners, respectively:

``` js

// NewTodoInput

// ...

methods: {

 addTodo: function () {

 eventHub.$emit('add-todo', { text: this.newTodoText })

 this.newTodoText = ''

 }

}

```

``` js

// DeleteTodoButton

// ...

methods: {

 deleteTodo: function (id) {

 eventHub.$emit('delete-todo', id)

 }

}

```

``` js

// Todos

// ...

created: function () {

 eventHub.$on('add-todo', this.addTodo)

 eventHub.$on('delete-todo', this.deleteTodo)

},

// It's good to clean up event listeners before

// a component is destroyed.

beforeDestroy: function () {

 eventHub.$off('add-todo', this.addTodo)

 eventHub.$off('delete-todo', this.deleteTodo)

},

methods: {

 addTodo: function (newTodo) {

 this.todos.push(newTodo)

 },

 deleteTodo: function (todoId) {

 this.todos = this.todos.filter(function (todo) {

 return todo.id !== todoId

 })

 }

}

```

This pattern can serve as a replacement for `$dispatch` and `$broadcast` in simple scenarios, but for more complex cases, it's recommended to use a dedicated state management layer such as [Vuex](https://github.com/vuejs/vuex).

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>$dispatch</code> and <code>$broadcast</code>.</p>

</div>

## Filters

### Filters Outside Text Interpolations <sup>deprecated</sup>

Filters can now only be used inside text interpolations (`{{ }}` tags). In the past we've found using filters within directives such as `v-model`, `v-on`, etc led to more complexity than convenience. For list filtering on `v-for`, it's also better to move that logic into JavaScript as computed properties, so that it can be reused throughout your component.

In general, whenever something can be achieved in plain JavaScript, we want to avoid introducing a special syntax like filters to take care of the same concern. Here's how you can replace Vue's built-in directive filters:

#### Replacing the `debounce` Filter

Instead of:

``` html

<input v-on:keyup="doStuff | debounce 500">

```

``` js

methods: {

 doStuff: function () {

 // ...

 }

}

```

Use [lodash's `debounce`](https://lodash.com/docs/4.15.0#debounce) (or possibly [`throttle`](https://lodash.com/docs/4.15.0#throttle)) to directly limit calling the expensive method. You can achieve the same as above like this:

``` html

<input v-on:keyup="doStuff">

```

``` js

methods: {

 doStuff: _.debounce(function () {

 // ...

 }, 500)

}

```

For more on the advantages of this strategy, see [the example here with `v-model`](#v-model-with-debounce-deprecated).

#### Replacing the `limitBy` Filter

Instead of:

``` html

<p v-for="item in items | limitBy 10">{{ item }}</p>

```

Use JavaScript's built-in [`.slice` method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice#Examples) in a computed property:

``` html

<p v-for="item in filteredItems">{{ item }}</p>

```

``` js

computed: {

 filteredItems: function () {

 return this.items.slice(0, 10)

 }

}

```

#### Replacing the `filterBy` Filter

Instead of:

``` html

<p v-for="user in users | filterBy searchQuery in 'name'">{{ user.name }}</p>

```

Use JavaScript's built-in [`.filter` method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter#Examples) in a computed property:

``` html

<p v-for="user in filteredUsers">{{ user.name }}</p>

```

``` js

computed: {

 filteredUsers: function () {

 return this.users.filter(function (user) {

 return user.name.indexOf(this.searchQuery)

 })

 }

}

```

JavaScript's native `.filter` can also manage much more complex filtering operations, because you have access to the full power of JavaScript within computed properties. For example, if you wanted to find all active users and case-insensitively match against both their name and email:

``` js

this.users.filter(function (user) {

 var searchRegex = new RegExp(this.searchQuery, 'i')

 return user.isActive && (

 searchRegex.test(user.name) ||

 searchRegex.test(user.email)

 )

})

```

#### Replacing the `orderBy` Filter

Instead of:

``` html

<p v-for="user in users | orderBy 'name'">{{ user.name }}</p>

```

Use [lodash's `orderBy`](https://lodash.com/docs/4.15.0#orderBy) (or possibly [`sortBy`](https://lodash.com/docs/4.15.0#sortBy)) in a computed property:

``` html

<p v-for="user in orderedUsers">{{ user.name }}</p>

```

``` js

computed: {

 orderedUsers: function () {

 return _.orderBy(this.users, 'name')

 }

}

```

You can even order by multiple columns:

``` js

_.orderBy(this.users, ['name', 'last_login'], ['asc', 'desc'])

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of filters being used inside directives. If you miss any, you should also see <strong>console errors</strong>.</p>

</div>

### Filter Argument Syntax

Filters' syntax for arguments now better aligns with JavaScript function invocation. So instead of taking space-delimited arguments:

``` html

<p>{{ date | formatDate 'YY-MM-DD' timeZone }}</p>

```

We surround the arguments with parentheses and delimit the arguments with commas:

``` html

<p>{{ date | formatDate('YY-MM-DD', timeZone) }}</p>

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the old filter syntax. If you miss any, you should also see <strong>console errors</strong>.</p>

</div>

### Built-In Text Filters <sup>deprecated</sup>

Although filters within text interpolations are still allowed, all of the filters have been removed. Instead, it's recommended to use more specialized libraries for solving problems in each domain (e.g. [`date-fns`](https://date-fns.org/) to format dates and [`accounting`](http://openexchangerates.github.io/accounting.js/) for currencies).

For each of Vue's built-in text filters, we go through how you can replace them below. The example code could exist in custom helper functions, methods, or computed properties.

#### Replacing the `json` Filter

You actually don't need to for debugging anymore, as Vue will nicely format output for you automatically, whether it's a string, number, array, or plain object. If you want the exact same functionality as JavaScript's `JSON.stringify` though, then you can use that in a method or computed property.

#### Replacing the `capitalize` Filter

``` js

text[0].toUpperCase() + text.slice(1)

```

#### Replacing the `uppercase` Filter

``` js

text.toUpperCase()

```

#### Replacing the `lowercase` Filter

``` js

text.toLowerCase()

```

#### Replacing the `pluralize` Filter

The [pluralize](https://www.npmjs.com/package/pluralize) package on NPM serves this purpose nicely, but if you only want to pluralize a specific word or want to have special output for cases like `0`, then you can also easily define your own pluralize functions. For example:

``` js

function pluralizeKnife (count) {

 if (count === 0) {

 return 'no knives'

 } else if (count === 1) {

 return '1 knife'

 } else {

 return count + 'knives'

 }

}

```

#### Replacing the `currency` Filter

For a very naive implementation, you could just do something like this:

``` js

'$' + price.toFixed(2)

```

In many cases though, you'll still run into strange behavior (e.g. `0.035.toFixed(2)` rounds up to `0.4`, but `0.045` rounds down to `0.4`). To work around these issues, you can use the [`accounting`](http://openexchangerates.github.io/accounting.js/) library to more reliably format currencies.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the deprecated text filters. If you miss any, you should also see <strong>console errors</strong>.</p>

</div>

### Two-Way Filters <sup>deprecated</sup>

Some users have enjoyed using two-way filters with `v-model` to create interesting inputs with very little code. While _seemingly_ simple however, two-way filters can also hide a great deal of complexity - and even encourage poor UX by delaying state updates. Instead, components wrapping an input are recommended as a more explicit and feature-rich way of creating custom inputs.

As an example, we'll now walk the migration of a two-way currency filter:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/6744xnjk/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

It mostly works well, but the delayed state updates can cause strange behavior. For example, click on the `Result` tab and try entering `9.999` into one of those inputs. When the input loses focus, its value will update to `$10.00`. When looking at the calculated total however, you'll see that `9.999` is what's stored in our data. The version of reality that the user sees is out of sync!

To start transitioning towards a more robust solution using Vue 2.0, let's first wrap this filter in a new `<currency-input>` component:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/943zfbsh/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

This allows us add behavior that a filter alone couldn't encapsulate, such as selecting the content of an input on focus. Now the next step will be to extract the business logic from the filter. Below, we pull everything out into an external [`currencyValidator` object](https://gist.github.com/chrisvfritz/5f0a639590d6e648933416f90ba7ae4e):

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/9c32kev2/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

This increased modularity not only makes it easier to migrate to Vue 2, but also allows currency parsing and formatting to be:

- unit tested in isolation from your Vue code

- used by other parts of your application, such as to validate the payload to an API endpoint

Having this validator extracted out, we've also more comfortably built it up into a more robust solution. The state quirks have been eliminated and it's actually impossible for users to enter anything wrong, similar to what the browser's native number input tries to do.

We're still limited however, by filters and by Vue 1.0 in general, so let's complete the upgrade to Vue 2.0:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/1oqjojjx/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

You may notice that:

- Every aspect of our input is more explicit, using lifecycle hooks and DOM events in place of the hidden behavior of two-way filters.

- We can now use `v-model` directly on our custom inputs, which is not only more consistent with normal inputs, but also means our component is Vuex-friendly.

- Since we're no longer using filter options that require a value to be returned, our currency work could actually be done asynchronously. That means if we had a lot of apps that had to work with currencies, we could easily refactor this logic into a shared microservice.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of filters used in directives like <code>v-model</code>. If you miss any, you should also see <strong>console errors</strong>.</p>

</div>

## Slots

### Duplicate Slots <sup>deprecated</sup>

It is no longer supported to have `<slot>`s with the same name in the same template. When a slot is rendered it is "used up" and cannot be rendered elsewhere in the same render tree. If you must render the same content in multiple places, pass that content as a prop.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run your end-to-end test suite or app after upgrading and look for <strong>console warnings</strong> about duplicate slots <code>v-model</code>.</p>

</div>

### `slot` Attribute Styling <sup>deprecated</sup>

Content inserted via named `<slot>` no longer preserves the `slot` attribute. Use a wrapper element to style them, or for advanced use cases, modify the inserted content programmatically using [render functions](render-function.html).

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find CSS selectors targeting named slots (e.g. <code>[slot="my-slot-name"]</code>).</p>

</div>

## Special Attributes

### `keep-alive` Attribute <sup>deprecated</sup>

`keep-alive` is no longer a special attribute, but rather a wrapper component, similar to `<transition>`. For example:

``` html

<keep-alive>

 <component v-bind:is="view"></component>

</keep-alive>

```

This makes it possible to use `<keep-alive>` on multiple conditional children:

``` html

<keep-alive>

 <todo-list v-if="todos.length > 0"></todo-list>

 <no-todos-gif v-else></no-todos-gif>

</keep-alive>

```

<p class="tip">When `<keep-alive>` has multiple children, they should eventually evaluate to a single child. Any child other than the first one will simply be ignored.</p>

When used together with `<transition>`, make sure to nest it inside:

``` html

<transition>

 <keep-alive>

 <component v-bind:is="view"></component>

 </keep-alive>

</transition>

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find <code>keep-alive</code> attributes.</p>

</div>

## Interpolation

### Interpolation within Attributes <sup>deprecated</sup>

Interpolation within attributes is no longer valid. For example:

``` html

<button class="btn btn-{{ size }}"></button>

```

Should either be updated to use an inline expression:

``` html

<button v-bind:class="'btn btn-' + size"></button>

```

Or a data/computed property:

``` html

<button v-bind:class="buttonClasses"></button>

```

``` js

computed: {

 buttonClasses: function () {

 return 'btn btn-' + size

 }

}

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of interpolation used within attributes.</p>

</div>

### HTML Interpolation <sup>deprecated</sup>

HTML interpolations (`{{{ foo }}}`) have been deprecated in favor of the [`v-html` directive](/api/#v-html).

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find HTML interpolations.</p>

</div>

### One-Time Bindings <sup>deprecated</sup>

One time bindings (`{{* foo }}`) have been deprecated in favor of the new [`v-once` directive](/api/#v-once).

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find one-time bindings.</p>

</div>

## Reactivity

### `vm.$watch`

Watchers created via `vm.$watch` are now fired before the associated component rerenders. This gives you the chance to further update state before the component rerender, thus avoiding unnecessary updates. For example, you can watch a component prop and update the component's own data when the prop changes.

If you were previously relying on `vm.$watch` to do something with the DOM after a component updates, you can instead do so in the `updated` lifecycle hook.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run your end-to-end test suite, if you have one. The <strong>failed tests</strong> should alert to you to the fact that a watcher was relying on the old behavior.</p>

</div>

### `vm.$set`

The former `vm.$set` behavior has been deprecated and it is now just an alias for [`Vue.set`](/api/#Vue-set).

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the deprecated usage.</p>

</div>

### `vm.$delete`

The former `vm.$delete` behavior has been deprecated and it is now just an alias for [`Vue.delete`](/api/#Vue-delete)

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the deprecated usage.</p>

</div>

### `Array.prototype.$set` <sup>deprecated</sup>

use Vue.set instead

(console error, migration helper)

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>.$set</code> on an array. If you miss any, you should see <strong>console errors</strong> from the missing method.</p>

</div>

### `Array.prototype.$remove` <sup>deprecated</sup>

Use `Array.prototype.splice` instead. For example:

``` js

methods: {

 removeTodo: function (todo) {

 var index = this.todos.indexOf(todo)

 this.todos.splice(index, 1)

 }

}

```

Or better yet, just pass removal methods an index:

``` js

methods: {

 removeTodo: function (index) {

 this.todos.splice(index, 1)

 }

}

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>.$remove</code> on an array. If you miss any, you should see <strong>console errors</strong> from the missing method.</p>

</div>

### `Vue.set` and `Vue.delete` on Vue instances <sup>deprecated</sup>

Vue.set and Vue.delete can no longer work on Vue instances. It is now mandatory to properly declare all top-level reactive properties in the data option. If you'd like to delete properties on a Vue instance or its `$data`, just set it to null.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.set</code> or <code>Vue.delete</code> on a Vue instance. If you miss any, they'll trigger <strong>console warnings</strong>.</p>

</div>

### Replacing `vm.$data` <sup>deprecated</sup>

It is now prohibited to replace a component instance's root $data. This prevents some edge cases in the reactivity system and makes the component state more predictable (especially with type-checking systems).

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of overwriting <code>vm.$data</code>. If you miss any, <strong>console warnings</strong> will be emitted.</p>

</div>

### `vm.$get` <sup>deprecated</sup>

Just retrieve reactive data directly.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$get</code>. If you miss any, you'll see <strong>console errors</strong>.</p>

</div>

## DOM-Focused Instance Methods

### `vm.$appendTo` <sup>deprecated</sup>

Use the native DOM API:

``` js

myElement.appendChild(vm.$el)

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$appendTo</code>. If you miss any, you'll see <strong>console errors</strong>.</p>

</div>

### `vm.$before` <sup>deprecated</sup>

Use the native DOM API:

``` js

myElement.parentNode.insertBefore(vm.$el, myElement)

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$before</code>. If you miss any, you'll see <strong>console errors</strong>.</p>

</div>

### `vm.$after` <sup>deprecated</sup>

Use the native DOM API:

``` js

myElement.parentNode.insertBefore(vm.$el, myElement.nextSibling)

```

Or if `myElement` is the last child:

``` js

myElement.parentNode.appendChild(vm.$el)

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$after</code>. If you miss any, you'll see <strong>console errors</strong>.</p>

</div>

### `vm.$remove` <sup>deprecated</sup>

Use the native DOM API:

``` js

vm.$el.remove()

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$remove</code>. If you miss any, you'll see <strong>console errors</strong>.</p>

</div>

## Meta Instance Methods

### `vm.$eval` <sup>deprecated</sup>

No real use. If you do happen to rely on this feature somehow and aren't sure how to work around it, post on [the forum](http://forum.vuejs.org/) for ideas.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$eval</code>. If you miss any, you'll see <strong>console errors</strong>.</p>

</div>

### `vm.$interpolate` <sup>deprecated</sup>

No real use. If you do happen to rely on this feature somehow and aren't sure how to work around it, post on [the forum](http://forum.vuejs.org/) for ideas.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$interpolate</code>. If you miss any, you'll see <strong>console errors</strong>.</p>

</div>

### `vm.$log` <sup>deprecated</sup>

Use the [Vue Devtools](https://github.com/vuejs/vue-devtools) for the optimal debugging experience.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$log</code>. If you miss any, you'll see <strong>console errors</strong>.</p>

</div>

## Instance DOM Options

### `replace: false` <sup>deprecated</sup>

Components now always replace the element they're bound to. To simulate the behavior of `replace: false`, you can wrap your root component with an element similar to the one you're replacing. For example:

``` js

new Vue({

 el: '#app',

 template: '<div id="app"> ... </div>'

})

```

Or with a render function:

``` js

new Vue({

 el: '#app',

 render: function (h) {

 h('div', {

 attrs: {

 id: 'app',

 }

 }, /* ... */)

 }

})

```

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>replace: false</code>.</p>

</div>

## Global Config

### `Vue.config.debug` <sup>deprecated</sup>

No longer necessary, since warnings come with stack traces by default now.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.config.debug</code>.</p>

</div>

### `Vue.config.async` <sup>deprecated</sup>

Async is now required for rendering performance.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.config.async</code>.</p>

</div>

### `Vue.config.delimiters` <sup>deprecated</sup>

This has been reworked as a [component-level option](/api/#delimiters). This allows you to use alternative delimiters within your app without breaking 3rd-party components.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.config.delimiters</code>.</p>

</div>

### `Vue.config.unsafeDelimiters` <sup>deprecated</sup>

HTML interpolation has been [deprecated in favor of `v-html`](#HTML-Interpolation-deprecated).

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.config.unsafeDelimiters</code>. After this, the helper will also find instances of HTML interpolation so that you can replace them with `v-html`.</p>

</div>

## Global API

### `Vue.extend` with `el` <sup>deprecated</sup>

The el option can no longer be used in `Vue.extend`. It's only valid as an instance creation option.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run your end-to-end test suite or app after upgrading and look for <strong>console warnings</strong> about the <code>el</code> option with <code>Vue.extend</code>.</p>

</div>

### `Vue.elementDirective` <sup>deprecated</sup>

Use components instead.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.elementDirective</code>.</p>

</div>

### `Vue.partial` <sup>deprecated</sup>

Partials have been deprecated in favor of more explicit data flow between components, using props. Unless you're using a partial in a performance-critical area, the recommendation is to simply use a [normal component](components.html) instead. If you were dynamically binding the `name` of a partial, you can use a [dynamic component](http://vuejs.org/guide/components.html#Dynamic-Components).

If you happen to be using partials in a performance-critical part of your app, then you should upgrade to [functional components](render-function.html#Functional-Components). They must be in a plain JS/JSX file (rather than in a `.vue` file) and are stateless and instanceless, just like partials. This makes rendering extremely fast.

A benefit of functional components over partials is that they can be much more dynamic, because they grant you access to the full power of JavaScript. There is a cost to this power however. If you've never used a component framework with render functions before, they may take a bit longer to learn.

<div class="upgrade-path">

 <h4>Upgrade Path</h4>

 <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.partial</code>.</p>

</div>


