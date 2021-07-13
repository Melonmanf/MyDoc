[TOC]

# JS



## 变量的作用域

### var

var函数内部的程序可以读取函数外部的var变量，但是外部变量无法读取外部函数的变量。如果隐式的申明（不加var，num=' ')，函数外部也可以访问函数内部的隐式var变量。再预解析阶段，变量会提升为object全局变量。



### let

块级作用域，外部程序无法访问内部作用域。



### const

作用域和let一致，不同的是const申明的变量是不可变的。



### 使用场景

1. 大多是场景使用const，除了后续变量会改变（如for循环）用let；
2. 用const声明常量，其他情况一律用let关键字；
3. var一般情况下不适用。





# Vue.js

[Vue API](https://cn.vuejs.org/v2/api/)

## 指令

| 语法                  | 释义                                          |
| :-------------------- | --------------------------------------------- |
| {{message}}           | 文本插值                                      |
| v-html                |                                               |
| v-text                |                                               |
| :title / v-bind:title |                                               |
| v-bind                | 动态绑定属性值                                |
| v-on: / @             | 绑定事件                                      |
| v-if                  | 作用的元素，从Dom中添加或删除元素；切换开销大 |
| v-show                | 显示和隐藏、display:none;初始化开销小         |
| v-for                 |                                               |
| v-model               | 在表单控件或者组件上创建双向绑定。            |
| v-pre                 | 显示原始 Mustache 标签                        |
| v-once                | 只渲染元素和组件**一次**                      |
|                       | 保持在元素上直到关联组件实例结束编译          |
|                       |                                               |



v-for

~~~html
<div v-for="(item, index) in items"></div>
<div v-for="(val, key) in object"></div>
<div v-for="(val, name, index) in object"></div>
~~~



 v-cloak

- **不需要表达式**

- **用法**：

  这个指令保持在元素上直到关联组件实例结束编译。和 CSS 规则如 `[v-cloak] { display: none }` 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到组件实例准备完毕。

- **示例**：

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

  

  <div> 不会显示，直到编译结束。



[按键修饰符](https://cn.vuejs.org/v2/guide/events.html#%E6%8C%89%E9%94%AE%E7%A0%81)



## Vue3 新特性

### 实例方式

```js
使用小型、独立和可复用的组件构建大型应用，几乎所有的应用界面都可抽象为一个组件树
<div id="todo-list-app">
    <ol>
        <todo-item v-for="item in groceryList" :todo="item" :key="item.id"></todo-item>

    </ol>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
    // 定义Vue应用
    const TodoList = {
        data() {
            return {
                groceryList: [
                    {id: 0, text: 'Vegetables'},
                    {id: 1, text: 'Cheese'},
                    {id: 2, text: 'Whatever else humans are supposed to eat'}
                ]
            }
        },

    }

    // 创建Vue应用
    const app = Vue.createApp(TodoList)

    // 添加Vue应用组件
    app.component('todo-item', {
        props: ['todo'],
        template: `
          <li>{{ todo.text }}</li>`
    })


    // 挂载Vue应用
    app.mount('#todo-list-app')
</script>
```





## 生命周期

[生命周期](https://v3.cn.vuejs.org/api/options-lifecycle-hooks.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90)

<font color="red">注意：</font>不要再`property`（属性）或*回调函数*上使用箭头函数。因为箭头函数并没有this，`this` 会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 `Uncaught TypeError: Cannot read property of undefined` 或 `Uncaught TypeError: this.myMethod is not a function` 之类的错误。

![生命周期](https://v3.cn.vuejs.org/images/lifecycle.svg)

## 模板语法

所有Vue.js的模板都是合法的HTML，所有能被准许规范的浏览器和HTML解析器解析。

### 插值

#### 文本

##### 文本插值	-	Mustache语法 {{ message }}
每个Mustache标签都对应一个property，无论何时，绑定在组件上的poperty的值改变了，插值内容就会更新。



##### v-once   
每个插值只能执行一次，之后数据改变了插值出不会改变。会影响该节点上的其他数据绑定。

```html
<span v-once>这个将不会改变: {{ msg }}</span>
```



#### 原始HTML	-	v-html

原始HTML    -    文本插值会把数据解释为普通文本，如果要插入HTML需要v-html指令

<font color="red">注意：</font>在你的站点上动态渲染任意的 HTML 是非常危险的，因为它很容易导致 [XSS 攻击](https://baike.baidu.com/item/XSS%E6%94%BB%E5%87%BB/954065?fr=aladdin)。请只对可信内容使用 HTML 插值，**绝不要**将用户提供的内容作为插值。



#### Attribute	-	v-bind

Mustache语法不能再HTML attribute中使用，所有用v-bind指令绑定attribute。

```html
<button v-bind:disabled="isButtonDisabled">按钮</button>
```



<font color="red">注意：</font>在 DOM 中使用模板时 (直接在一个 HTML 文件里撰写模板)，还需要避免使用大写字符来命名键名，因为浏览器会把 attribute 名全部强制转为小写

#### JS表达式

Vue.js提供了JS表达式的支持，但是赋值等语法是不支持的

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

注意：模板表达式都被放在沙盒中，只能访问一个[受限的列表](https://github.com/vuejs/vue-next/blob/master/packages/shared/src/globalsWhitelist.ts#L3)，如 `Math` 和 `Date`。你不应该在模板表达式中试图访问用户定义的全局变量。



### 指令

指令(Directives)带有`v-`前缀的特殊attribute。指令attribute的值预期是单个的Js表达式（v-if、v-for除外）

指令的职责：当表达式的值改变时，其会有连带影响，响应式地作用DOM。



##### 参数

`v-bind` 指令可以用于响应式地更新 HTML attribute：`href` 是参数，告知 `v-bind` 指令将该元素的 `href` attribute 与表达式 `url` 的值绑定

```html
<a v-bind:href="url"> ... </a>
```

`v-on` 指令，它用于监听 DOM 事件：

```html
<a v-on:click="doSomething"> ... </a>
```



##### 动态参数

`attributeName` 会被作为一个 JavaScript 表达式进行动态求值，求得的值将会作为最终的参数来使用。

```html
<a v-bind:[attributeName]="url"> ... </a>
```

```html
<a v-on:[eventName]="doSomething"> ... </a>
```

<font color="red">注意：</font>

* 动态参数值约定：动态参数预期会求出一个字符串，异常情况下值为 `null`。这个特殊的 `null` 值可以被显性地用于移除绑定。任何其它非字符串类型的值都将会触发一个警告。
* 动态参数表达式约定：动态参数表达式有一些语法约束，因为某些字符，如空格和引号，放在 HTML attribute 名里是无效的。变通的办法是使用没有空格或引号的表达式，或用[计算属性](https://v3.cn.vuejs.org/guide/computed.html)替代这种复杂表达式。

##### 修饰符

修饰符 (modifier) 是以半角句号 `.` ，例如，`.prevent` 修饰符告诉 `v-on` 指令对于触发的事件调用 `event.preventDefault()`

```html
<form v-on:submit.prevent="onSubmit">...</form>
```



### 缩写

v-bind	-	:

v-on	-	@



## Data Property和方法

### Data property

组件的data是一个函数，再创建新的组件实例后会调用此函数，它会返回一个对象，vue会响应性将其包裹，以$data的形式存储再组件实例中。

对尚未提供所需值的property使用null、nudefind或其他占位符

**没有在data中定义的property**可以添加到组件实例中，Vue响应式系统不会自动跟踪他（无法响应式渲染数据 ）

避免用`$`、`_`前缀、`$`通过组件暴露自己的内置API，`_`是内部property



### methods

Vue 自动为 `methods` 绑定 `this`，以便于它始终指向组件实例。这将确保方法在用作事件监听或回调时保持正确的 `this` 指向。在定义 `methods` 时应避免使用箭头函数，因为这会阻止 Vue 绑定恰当的 `this` 指向。

1. 通常被当做事件监听使用
2. 可以直接从模板中调用方法



```html
<span :title="toTitleDate(date)">
  {{ formatDate(date) }}
</span>
```



### 防抖节流

Vue 没有内置支持防抖和节流，但可以使用 [Lodash](https://lodash.com/) 等库来实现。

```html
<script src="https://unpkg.com/lodash@4.17.20/lodash.min.js"></script>
<script>
app.component('save-button', {
  created() {
    // 用 Lodash 的防抖函数
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // 移除组件时，取消定时器
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... 响应点击 ...
    }
  },
  template: `
    <button @click="debouncedClick">
      Save
    </button>
  `
})
</script>
```

[防抖和节流](https://blog.csdn.net/weixin_33905756/article/details/91415517)

**防抖（debounce）**：当持续触发某事件时，一定时间间隔内没有再触发事件时，事件处理函数才会执行一次，如果设定的时间间隔到来之前，又一次触发了事件，就重新开始延时。

**节流（throttle）**：当持续触发事件时，有规律的每隔一个时间间隔执行一次事件处理函数。



## 计算属性和侦听器

### 计算属性

模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护。

所以，对于任何包含响应式数据的复杂逻辑，都应该使用**计算属性**

```html
<div id="computed-basics">
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</div>
```



```js
Vue.createApp({
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  },
  computed: {
    // 计算属性的 getter
    publishedBooksMessage() {
      // `this` 指向 vm 实例
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
}).mount('#computed-basics')
```



<font color="#0099ff">计算属性和方法的区别：</font>

两种方式的最终结果确实是完全相同的。然而，不同的是**计算属性是基于它们的反应依赖关系缓存的**。计算属性只在相关响应式依赖发生改变时它们才会重新求值。

这就意味着只要 `author.books` 还没有发生改变，多次访问 `publishedBookMessage` 计算属性会立即返回之前的计算结果，而不必再次执行函数。

这也同样意味着下面的计算属性将不再更新，因为 **Date.now () 不是响应式依赖**

如果你不希望有缓存，请用 `method` 来替代

#### 计算属性的getter()

计算属性默认只有 getter，不过在需要时你也可以提供一个 setter

```js
computed: {
  fullName: {
    // getter
    get() {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set(newValue) {
      const names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

现在再运行 `vm.fullName = 'John Doe'` 时，setter 会被调用，`vm.firstName` 和 `vm.lastName` 也会相应地被更新。



### 侦听器

虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 `watch` 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="watch-example">
    <p>
        Ask a yes/no question:
        <input v-model="question"/>
    </p>
    <p>{{ answer }}</p>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script>
    const watchExampleVM = Vue.createApp({
        data() {
            return {
                question: '',
                answer: 'Questions usually contain a question mark. ;-)'
            }
        },
        watch: {
            // whenever question changes, this function will run
            question(newQuestion, oldQuestion) {
                console.log(newQuestion.indexOf('?'))
                if (newQuestion.indexOf('?') > -1) {
                    this.getAnswer()
                }
            }
        },
        methods: {
            getAnswer() {
                this.answer = 'Thinking...'
                axios
                    .get('https://yesno.wtf/api')
                    .then(response => {
                        this.answer = response.data.answer
                    })
                    .catch(error => {
                        this.answer = 'Error! Could not reach the API. ' + error
                    })
            }
        }
    }).mount('#watch-example')
</script>
</body>
</html>
```

使用 `watch` 选项允许我们执行异步操作 (访问一个 API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。



**侦听属性：** Vue 提供的一种更通用的方式来观察和响应当前活动的实例上的数据变动

，有一些数据需要随着其它数据变动而变动时





## 事件处理

事件处理程序可以有多个方法，这些方法由逗号隔开。

```html
<button @click="one($event), two($event)">
  Submit
</button>
```



### 事件修饰符

为了解决方法只处理数据逻辑，而不处理DOM细节。

- `.stop` 阻止单击事件继续传播
- `.prevent`提交事件不再重载页面
- `.capture` 添加事件监听器时使用事件捕获模式，即内部元素触发的事件先在此处理，然后才交由内部元素进行处理
- `.self`事件不是从内部元素触发的
- `.once`事件将只会触发一次
- `.passive`滚动事件的默认行为 (即滚动行为) 将会立即触发， 而不会等待 `onScroll` 完成，这其中包含 `event.preventDefault()` 的情况

<font color="red">注意：</font>使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 `v-on:click.prevent.self` 会阻止所有的点击，而 `v-on:click.self.prevent` 只会阻止对元素自身的点击。



### 按键修饰符

@keyup



**按键别名**

- `.enter`
- `.tab`
- `.delete` (捕获“删除”和“退格”键)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`







### 系统修饰符

可以用如下修饰符来实现仅在按下相应按键时才触发鼠标或键盘事件的监听器。

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

<font color="red">注意：</font>在 Mac 系统键盘上，meta 对应 command 键 (⌘)。在 Windows 系统键盘 meta 对应 Windows 徽标键 (⊞)。在 Sun 操作系统键盘上，meta 对应实心宝石键 (◆)。在其他特定键盘上，尤其在 MIT 和 Lisp 机器的键盘、以及其后继产品，比如 Knight 键盘、space-cadet 键盘，meta 被标记为“META”。在 Symbolics 键盘上，meta 被标记为“META”或者“Meta”。



vue绑定事件的优点在于，可以不用在JS绑定事件，只需要关注前段业务逻辑代码即可，实现了Dom的解耦。







## 组件

使用小型、独立和可复用的组件构建大型应用，几乎所有的应用界面都可抽象为一个组件树。

类似于自定义元素，Vue参考了Web components规范，但是有几个关键差别：

1. Web Components规范未被所有浏览器支持，而Vue可以在所有浏览器之下表现一直（IE11除外），Vue组件不需任何polyfill(腻子脚本)。
2. Vue组件提供了存自定义元素不具备的一些重要功能，最突出的的跨组件数据流、自定义时间通信以及构建工具集成。



<font color="red">注意：</font>

* Vue 内部没有使用自定义元素
* 能将数据从父组件传入子组件
* 在定义组件template模板时，必须使用单个根元素将所有的布局结构包裹起来。
* data必须是一个函数，函数体内部返回一个对象结构。因为组件是可被复用的，当一个组件创建多个实例使用时，data不是函数而是普通的对象，则多个实例会共享一个data对象，会导致一个实例对象更新后，其他组件实例也会受到影响。



子组件可以通过调用内建的 [**$emit** 方法](https://v3.cn.vuejs.org/api/instance-methods.html#emit)并传入事件名称来触发一个事件



强烈推荐遵循 [W3C 规范](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name)中的自定义组件名 (字母全小写且必须包含一个连字符)。这会帮助你避免和当前以及未来的 HTML 元素相冲突。

```js
app.component('custom-input', {
    // 向子组件传递数据
    props: ['modelValue'],
    // 列出已抛出的事件
    emits: ['update:modelValue'],
    // <input v-model="value">
    template: `
      <input :value="modelValue" @input="$emit('update:modelValue',$event.target.value)">
    `,
    computed: {
        value: {
            get() {
                return this.modelValue
            },
            set(value) {
                this.$emit('update:modelValue', value)
            }
        }
    }
})
```

### 根组件

传递给createApp的选项（配置）用于配置根组件，挂载应用是，根组件用作渲染的起点。



### 单向数据流

父子组件之间是**单向下行绑定**的，父级prop的更新向下流动到子组件中，但子组件无法向上流动到父组件中。这样设计的原因是为了防止子组件意外变更父级组件的状态，从而导致应用的数据难以理解。

<font color="red">注意：</font>每次父组件变更时，子组件也会跟着刷新。



### 非Prop的Attribute





#### 禁用Attribute继承

`inheritAttrs:false`，应用于根节点之外的其他元素，不希望根元素继承Attribute



#### 多个根节点上的Attribue继承

与单个节点组件不同，具有多个根节点的组件不具有自动attribute回退行为。未显示绑定`$attrs`，将会发出运行时警告。



### 自定义事件

在组件上定义了原生事件侦听器，那么组件中的定义的事件会代替原生的事件侦听器。



#### 验证抛出的事件

emits使用对象语法，将会为事件分配一个函数，该函数接收传递给 `$emit` 调用的参数，并返回一个布尔值以指示事件是否有效。



#### 多个值v-model绑定

```html
<div id="app">
    <user-name
            v-model:first-name="firstName"
            v-model:last-name="lastName"
    ></user-name>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
    componentUserName = {
        props: {
            firstName: String,
            lastName: String
        },
        emits: ['update:firstName', 'update:lastName'],
        template: `
          <input
              type="text"
              :value="firstName"
              @input="$emit('update:firstName', $event.target.value)">

          <input
              type="text"
              :value="lastName"
              @input="$emit('update:lastName', $event.target.value)">
        `

    }

    const app = Vue.createApp({
        data() {
            return {
                firstName: 'John',
                lastName: 'Doe',
            }
        },
        components: {
            'user-name': componentUserName,
        }
    }).mount('#app')
</script>
```



#### 处理v-model修饰符

vue3.x中，v-model的修饰符通过`modelModelfiers` prop提供给组件。创建了组件他props中默认包含了为空对象的`modelModifiers` prop。

<font color="red">注意：</font>在组件的`created`生命周期钩子触发时，modelModifiers prop自定义的修饰符函数，返回值为`true`，因为自定义修饰符函数再被调用的时候绑定上了`v-model`

对于带参数的v-model绑定，生成的prop名称为 `arg + "Modifiers"`



### 插槽	\<slot\>

Vue实现了内容分发的API，将\<slot\>元素作为承载分发内容的出口。不仅可以作用于字符串，还可以是HTML或其他组件。当组件渲染时，会见\<slot\>\</slot\>替换为相应的内容



#### 渲染作用域

插槽可以访问与其余部分相同的实例property（相同的作用域）。

<font color="#00aaff">准则：</font>父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的



#### 备用内容

备用内容放在\<slot\>标签内，在没有提供内容的时候渲染。





#### 具名内容

需要多个插槽时，可以给*\<slot\>*标签加入attribute:name来定义插槽。

template标签只是为内容分组，不会渲染到页面中。

渲染的HTML

```html
<div class="container">
  <header>
    <!-- 我们希望把页头放这里 -->
  </header>
  <main>
    <!-- 我们希望把主要内容放这里 -->
  </main>
  <footer>
    <!-- 我们希望把页脚放这里 -->
  </footer>
</div>
```

模板

```html
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
```

<font color="#00aaff">列外情况：</font>\<template\>标签上加上`v-slot`指令，可以定义插槽

模板

```html
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

渲染的HTML

```html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```





#### 作用域插槽

绑定在 `<slot>` 元素上的 attribute 被称为**插槽 prop**

<font color="red">注意：</font>默认插槽的缩写语法**不能**和具名插槽混用，因为它会导致作用域不明确





#### 具名插槽的缩写

`v-slot:`	-	`#`

```html
<base-layout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <template #default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```





###  Privide / Inject

解决深度嵌套组件的prop沿着组件链逐级传递带来的繁琐。

provide，父组件用provie选项来提供数据

inject，子组件用inject选项来使用数据



### 处理响应性

provid/inject绑定默认并不是响应式的。给provide中需要响应式的参数分配以组合式API **computed** property

```js
app.component('todo-list', {
  // ...
  provide() {
    return {
      todoLength: Vue.computed(() => this.todos.length)
    }
  }
})

app.component('todo-list-statistics', {
  inject: ['todoLength'],
  created() {
    console.log(`Injected property: ${this.todoLength.value}`) // > Injected property: 5
  }
})
```



### 动态组件和异步组件

#### 动态组件

避免组件之间切换造成的反复渲染的性能问题，使用\<key-value\>标签将东南台组件包裹起来。即可将创建的组件缓存起来，在二次使用时，保留用户对该组件的操作和浏览位置。

```vue-html
<keep-alive>
  <component :is="currentTabComponent"></component>
</keep-alive>
```



#### 异步组件



###  组件通信

#### 父子组件通信

* 父传子：通过属性传递，使用props
* 子传父：使用事件，父组件使用子组件标签时，绑定自定义的事件，在子组件自定义时，调用`$emit()`方法去触发在父组件中的绑定的自定义事件。

#### 跨组件通信

* 可转为父子组件的通信方式
* event-bus 事件总线：

创建一个空的Vue实例

使用`$on`绑定事件、`$emit`触发事件

在需要接受数据的组件中，调用`$on`绑定事件

在需要传递数据的组件中，调用`$emit`触发事件









## 过渡&动画



### 基于class的动画和过渡

[CSS动画](https://www.w3school.com.cn/css/css3_animations.asp)

[CSS过渡](https://www.w3school.com.cn/css/css3_transitions.asp)

[animate.style]: animate.style



## 可复用和组合

处理大型应用时，共享和重用代码变得尤为重要。

组合式API：将同一个逻辑关注点相关代码手机在一起。

### setup

在组建创建之前执行，一旦`props`别解析，就会作为组合式API的入口。

<font color="red">注意：</font>在 `setup` 中你应该避免使用 `this`，因为它不会找到组件实例。`setup` 的调用发生在 `data` property、`computed` property 或 `methods` 被解析之前，所以它们无法在 `setup` 中被获取。



### 带`ref`的响应式变量

引用组件或Dom元素







## v-model原理

用object.defineProperty来进行数据劫持 ，再setter方法中检测数据修改后，能够重新渲染页面。





## AJAX

### [XMLHttRequerst()](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)

1. 创建XMLHttpRequest对象实例
2. 初始化链接，打开xhr.open(method,url,async)
3. 发送请求xhr.send(querystring),表单请求post提交数据，在send()方法调用之前还需要调用xhr.setRequestHeader('xx:xxx')
4. 处理响应xhr.onreadystatechange=function() {}
   * xhr.readySaate	-	就绪状态码0-4，4表示请求处理完毕，响应就绪。
   * xhr.status    -    HTTP状态码/
   * xhr.responseText    -    响应文本(JSON)

### [Promise对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

三种状态：pending - 初始化（等待）、fulfilled（成功）、rejected（失败）

~~~js
new Promise((resolve, reject) => {
    setTimeout(() => {
        if (Math.random() > 0.6){
            resolve(successData)
        }else{
            reject(error)
        }
    }, 1000)
}).then((successData) => {
    // 成功
}).then((error) => {
    
})
~~~





### [async/await](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/await)



`await` 操作符用于等待一个[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 对象。它只能在异步函数 [`async function`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function) 中使用。



await只能在async修饰的函数中使用





### [axios](http://axios-js.com/)

Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。

```
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```



### [fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)





### 跨域

同源策略：安全策略

同源：协议、域名、端口、

跨域解决方案：CORS/JSONP/方向代理/流浪器插件



## Vue Router

路由：最先从后端只能引入，根据不同的url请求服务器端的资源，有服务器web server 返回对应的页面的详细html数据到浏览器中渲染。

前端路由：在页面不完全刷新的情况下，实现页面切换，主要就是利用JS中的功能，前端路由实现模式通常有：hash或history API





### 基本配置

安装

~~~sh
npm install vue-router
~~~





# nodejs

## npm

### 更换镜像源

在命令行中设置

~~~sh
npm config set registry https://registry.npm.taobao.org/
~~~

查看镜像源

~~~sh
npm config get registry 
~~~







<font color="gray">注：本文纯用于个人学习笔记。</font>

