# Learning Vue.js
> 参考 https://cn.vuejs.org/v2/guide/
> , Last edit at May 1st. Start at April 27th.

## Installation

* \<script\>直接引入
1. 开发版本：https://vuejs.org/js/vue.js
2. 生产版本：https://vuejs.org/js/vue.min.js
3. 指定版本号：https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js, (在 //cdn.jsdelivr.net/npm/vue 浏览整个包)
4. 其他：[unpkg](https://unpkg.com/vue@2.5.16/dist/vue.js)或者[cdnjs](https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.16/vue.js)

* npm安装, 以配合webpack或者browsify模块打包器
```
// 最新稳定版
npm i vue
```

* [CLI](https://github.com/vuejs/vue-cli)
```
npm i -g vue-cli
vue init webpack v-myproject
cd v-myproject
npm run dev
```

* 其他
  - 完整版：包括编译器和运行时版本，运行时版本比完整版小约30%
  - UMD（\<script\>引入），CommonJS（配合打包工具如Browserify或Webpack1），ES Module（配合现代打包工具如Webpack2或Rollup）
  - UMD版本提供未压缩和压缩版本，而且可以直接用于AMD模块加载器 （Require.js）
  - 对于CommonJS和ES Module版本，需要自己压缩发布，官方不提供压缩版本
  - 手动构建, git clone https://github.com/vuejs/vue.git, run "npm i & npm run build"

## Introduction
> Vue核心库只关注视图层, 与各种流行类库结合使用，为SPA提供驱动

### 环境搭建
* 开发版, \<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"\>\</script\>
* 生产版, \<script src="https://cdn.jsdelivr.net/npm/vue"\>\</script\>

### 声明式渲染
```
<div id="app">
    {{ message }}
</div>

let app = new Vue({
    el: "#app",
    data: {
        message: "Hello world"
    }
});
```
* HTML相当于模板，Javascript定义组件
* 数据和DOM已经建立了连接（响应式的）
* app.message改变将触发UI上的更新

```
<div id="app">
    <span v-bind:title="message">鼠标悬停查看提示信息</span>
</div>
```
* v-bind称为指令（指令都带有前缀v-），`v-`是Vue提供的特殊特性
* 特殊的响应式行为

### 条件与循环渲染
```
<div id="app">
    <p v-if="seen">出现</p>
</div>

let app = new Vue({
    el: "#app",
    data: {
        seen: true
    }
});
```
* app.seen的改变引起DOM结构变化（元素显示隐藏）
* 插入/更新/移除元素可以用到[过渡&动画效果](https://cn.vuejs.org/v2/guide/transitions.html)

```
<div id="app">
    <ol>
        <li v-for="todo in todos">
            {{ todo.text }}
        </li>
    </ol>
</div>

let app = new Vue({
    el: "#app",
    data: {
        todos: [
            { text: "Vue" },
            { text: "React" },
            { text: "Angular" }
        ]
    }
});
```
* `v-for`，渲染一个列表

### 处理用户输入
```
<div id="app">
    <p>{{ message }}</p>
    <button v-on:click="reverseMessage">逆序字符串</button>
</div>

let app = new Vue({
    el: "#app",
    data: {
        message: "Hello"
    },
    methods: {
        reverseMessage: function() {
            this.message = this.message.split("").reverse().join("");
        }
    }
});
```
* v-on指令用来添加事件监听器

```
<div id="app">
    {{ message }}
    <input v-model="message" type="text" />
</div>

let app = new Vue({
    el: "#app",
    data: {
        message: "Hello"
    }
});
```
* v-model实现**表单**输入和数据的双向绑定

### 组件化构建
> 仔细想想，几乎任意类型的应用界面，都可以抽象成一个组件树。在Vue中, 一个组件就是一个预定义的Vue实例
```
// 定义名为 todo-item 的组件
Vue.component("todo-item", {
    // 自定义的属性列表，目前只有一个 "todo"
    props: [ "todo" ],
    // 使用自定义的属性 "todo"
    template: "<li>{{ todo.text }}</li>"
});

<ol>
    <todo-item
        v-for="item in items"
        v-bind:todo="item"
        v-bind:key="item.id">
    ></todo-item>
</ol>

let app = new Vue({
    el: "#app",
    data: {
        items: [
            { id: 0, text: "React" },
            { id: 1, text: "Vue" },
            { id: 3, text: "Angular" }
        ]
    }
});
```
* `props: [ "todo" ]`, 当前组件接收一个名为"todo"的属性
* `v-bind:todo="item"`, 为当前组件的属性"todo"绑定item

## Vue实例
### 创建一个Vue实例, 下面是完整的参数列表
```
let vm = new Vue({
    // 选项或参数
});
```
* 数据: `data, props, propsData, computed, methods, watch`
* DOM: `el, template, render, renderError`
* 生命周期钩子: `beforeCreate, created, beforeMount, mounted, beforeUpdate, updated, activated, deactivated, beforeDestroy, destroyed, errorCaptured`
* 资源: `directives, filters, components`
* 组合: `parent, mixins, extends, provide/inject`
* 其他: `name, delimeters, functional, model, inheritAttrs, comments`

### 数据与方法
```
let data = { a: 1 };
let vm = new Vue({ data: data });

vm.a === data.a;
vm.a = 10; // data.a === 10
data.a = 20; // vm.a === 20
```
* `data`对象中的属性才是响应式的，同时也是**Vue实例的属性!!!**
* 这些数据的改变, 会引起绑定的视图的重新渲染
* Object.freeze(data); 整个响应系统将不会工作(变化不会引起更新)
* 实例属性和方法, 前缀加$, 方便与用户定义属性区分开
```
vm.$data === data;
vm.$el === document.getElementById("app");
vm.$watch("a", function(newVal, oldVal) {
    // 当data.a改变之后被调用执行
});
```
* 实例属性和方法的完整列表
  * 属性: `$data, $props, $el, $options, $parent, $root, $children, $slots, $scopedSlots, $refs, $isServer, $attrs, $listeners`
  * 方法/数据: `$watch, $set, $delete`
  * 方法/事件: `$on, $once, $off, $emit`
  * 方法/生命周期: `$mount, $forceUpdate, $nextTick, $destroy`

### Vue实例生命周期钩子
```
let app = new Vue({
    data: { a: 1 },
    created: function() {
        // this.a === app.a !!!
        // 实例被创建之后被执行
    }
});
```
* 钩子函数内的this指向调用它的Vue实例
* **不要在属性或回调上使用箭头函数, 因为箭头函数是和父级上下文绑定的, 导致this不是预期的实例**
* 生命周期钩子:
  - beforeCreate (实例化之后, data observer & event/watcher配置之前)
  - created (挂载开始之前)
  - beforeMount (render首次调用)
  - mounted (el被vm.$el替换, DOM渲染完成? DOM和实例建立连接?)
  - beforeDestroy, destroyed (实例销毁, 实例和所有东西解除绑定, 移除事件监听, 所有子实例被销毁)
  - beforeUpdate (数据更新, 虚拟DOM修改之前)
  - updated (数据更新并导致DOM重绘之后)
  - activated, deactivated
  - errorCaptured (来自子孙组件的错误, 返回false阻止向上传播)

![](_images/lifecycle-vue.png)

## 模板语法
* Vue使用了基于HTML的模板语法, 声明式语法将DOM和Vue实例数据绑定
* Vue将模板编译成虚拟DOM渲染函数, 结合响应式系统, Vue能够计算出最少需要重新渲染多少组件, 并把DOM操作次数剪到最少
* 也可以不使用模板，直接实现render函数, 使用JSX语法 (Babel插件)

### 插值 (Mustache语法, 双大括号)
* 文本
```
<span>Message: {{ msg }}</span>
<span v-once>只能执行一次插值, 即使msg变化这里也不更新: {{ msg }}</span>
```

* 原始HTML, 动态渲染任意HTML非常危险, 很容易导致XSS攻击
```
let data = { rawHtml: "<span style='color:red'>Red</span>" };
// ...

<p>{{ rawHtml }}</p>    // <p><span...></p>
<p v-html="rawHtml"></p>    // <p>Red</p>
```

* 特性 (HTML原生属性)，需要使用`v-bind`指令
```
<div v-bind:id="dynamicId"></div>
<button v-bind:disabled="isBtnDisabled">Button</button>
```

* Javascript表达式 (语句不会生效)
> 不要试图访问用户定义的全局或者父级变量, 可以访问Math|Date之类的白名单里的全局变量
```
{{ number + 1 }}
{{ ok ? "yes" : "no" }}
{{ msg.split('') }}
<div v-bind:id="'list-' + id"></div>

// 如下为语句, 不会起作用
{{ var i = 1 }}
{{ if (ok) { return "OK" } }}
```

### 指令 (Directives, 带有`v-`前缀的特殊属性, 是Vue特有的)
* 参数 & 修饰符(.)
```
// v-for是特殊属性
<li v-for="item in items">{{ item.text }}</li>
<p v-if="seen">Show or Hide</p>
<a v-bind:href="url">...</a>
<a v-on:click="doSth">...</a>
// 被触发的事件里调用event.prevent()方法
<form v-on:submit.prevent="onSubmit">...</form>
```

### 缩写 (v-bind和v-on会被大量使用, 故提供特定简写)
```
v-bind:id  === :id
v-on:click === @click
```

## 计算属性和侦听器

> 方法`methods`，计算属性`computed`，侦听器`watch`

```
// 下面这段模板加入了复杂的逻辑，使得模板过重难以维护
// 这时可以将复杂逻辑放入到计算属性

<div id="example">
    {{ message.split('').reverse().join('') }}
</div>
```

### 计算属性

* 计算属性的简单例子
```
<div id="example">
    <p>Original message: {{ message }}</p>
    <p>Computed message: {{ reversedMessage }}</p>
</div>

var vm = new Vue({
    el: "#example",
    data: {
        message: "Hello"
    },
    computed: {
        // Getter of "reversedMessage"
        reversedMessage: function() {
            return this.message.split("").reverse().join("");
        }
    }
});

// 结果
// Original message: Hello
// Computed message: olleH

vm.reversedMessage; // olleH
vm.message = "World";
vm.reversedMessage; // dlroW
```

`vm.message`和`vm.reversedMessage`已经建立了依赖关系；并且计算属性的`getter`函数是没有副作用的，使其更易于测试和理解

* 计算属性缓存 VS 方法
```
<p>{{ fnReversedMessage() }}</p>

// ...
methods: {
    fnReversedMessage: function() {
        return this.message.split("").reverse().join("");
    }
}
computed: {
    now: function() {
        // Date.now()不是响应式依赖，所以一直显示一个值而不会发生变化
        return Date.now();
    }
}
```
  - 计算属性只有在其**相关依赖发生变化才更新**，即`message`变化才会引起`reversedMessage`变化，所以依赖不变化，值一直被缓存
  - 每当触发重绘时，方法总会再次被调用
  - 对于性能开销比较大的属性，计算属性更合适，如果不希望被缓存则使用方法

* 计算属性 VS 侦听属性（watch）

当一个属性需要随其他数据变动而变动时，很容易想到`watch`，不过通常更好的做法是使用`computed`

```
<div id="demo">{{ fullName }}</div>

var vm = new Vue({
    el: "#demo",
    data: {
        firstName: "Tom",
        lastName: "Cat",
        fullName: "Tom Cat"
    },
    // 使用侦听器，代码重复
    watch: {
        // 侦听 "firstName"，并改变 "fullName"
        firstName: function(val) {
            this.fullName = val + " " + this.lastName;
        },
        // 侦听 "lastName"，并改变 "fullName"
        lastName: function(val) {
            this.fullName = this.firstName + " " + val;
        }
    }
});

// 使用计算属性，代码更加简洁
data: {
    firstName: "Tom",
    lastName: "Cat"
},
// 使用计算属性，当 "firstName" 或 "lastName" 变化时更新 "fullName"
computed: {
    fullName: function() {
        return this.firstName + " " + this.lastName;
    }
}
```
* 计算属性的`setter`

> 计算属性默认只有`getter`

```
// 计算属性的完整写法
computed: {
    fullName: {
        get: function() {
            return this.firstName + " " + this.lastName;
        },
        set: function(newValue) {
            var names = newValue.split(" ");
            this.firstName = names[0];
            this.lastName = names[1];
        }
    }
}

// 调用set方法，vm.firstName和vm.lastName会被更新
vm.fullName = "John Doe";
```

### 侦听器

当需要在数据变化时执行异步操作或开销较大操作时，侦听器比较有效

```
<div id="app">
    <p><input v-model="question"></p>
    <p>{{ answer }}</p>
</div>

var vm = new Vue({
    el: "#app",
    data: {
        question: "",
        answer: "Wait for a response from server"
    },
    watch: {
        question: function(newQuestion, oldQuestion) {
            this.answer = "Waiting...";
            this.delayGetAnswer();
        }
    },
    created: function() {
        // 生命周期钩子 created
        // _.debounce是Lodash限制操作频繁的函数
        this.delayGetAnswer = _.debounce(this.getAnswer, 500);
    },
    methods: function() {
        getAnswer: function() {
            this.answer = "Thinking...";
            var that = this;
            ajax.get("http://...")
                .then(function(resp) {
                    vm.answer = _.capitalize(resp.data.answer);
                })
                .catch(function(err) {
                    vm.answer = "Error!";
                })
        }
    }
});
```

> 侦听器和计算属性效果上差别不大，但要依具体使用场景选择使用

## Class与Style绑定

操作元素的class和style属性很常见，因为都是属性所以可以用`v-bind`操作，但操作字符串不方便而且易出错，所以Vue对这两个属性做了专门的增强，可以是对象或数组

### 绑定 HTML Class
* 语法
1. 内联方式
```
<div v-bind:class="{ active: isActive }"></div>

data: {
    isActive: true
}

// 渲染结果
<div class="active"></div>
```

> `isActive`为[Truthy](https://developer.mozilla.org/zh-CN/docs/Glossary/Truthy)即为真（因为Javascript脚本语言将条件语句中非Boolean类型强制转换为Boolean，所以非false/""/NaN/0/null/undefined为真）

2. 多个class并存
```
<div class="static"
    v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>

data: {
    isActive: true,
    hasError: false
}

// 渲染结果
<div class="static active"></div>
```

3. 非内联方式
```
<div v-bind:class="classObject"></div>

data: {
    classObject: {
        active: true,
        'text-danger': false
    }
}

// 渲染结果
<div class="active"></div>
```

4. 返回计算属性（常用）
```
<div v-bind:class="classObject"></div>

computed: {
    classObject: function() {
        return {
            active: this.isActive && !this.error,
            'text-danger': this.error && this.error.type === 'fetal'
        }
    }
}

// 渲染结果
<div class="active"></div>
```

* 数组语法
1. 基础例子
```
<div v-bind:class="[activeClass, errorClass]"></div>

data: {
    activeClass: "active",
    errorClass: "text-danger"
}

// 渲染结果
<div class="active text-danger"></div>
```

2. 三元表达式
```
<div v-bind:class="[isActive ? activeClass : '', errorClass ]"></div>

data: {
    isActive: false
}

// 渲染结果
<div class="errorClass"></div>
```

 3. 更简洁的方式
```
<div v-bind:class="[ {activeClass: isActive}, errorClass]"></div>

data: {
    isActive: true
}

// 渲染结果
<div class="activeClass errorClass"></div>
```

* 应用于组件
```
// 定义一个组件
Vue.component("my-component", {
    template: "<p class='foo bar'></p>"
});
```

1. 添加class
```
// 使用组件
<my-component class="a b"></my-component>

// 渲染结果
<p class="foo bar a b"></p>
```

2. 带数据绑定`class`
```
// 使用组件
<my-component v-bind:class="{active: isActive}"></my-component>

data: {
    isActive: true
}

// 渲染结果
<p class="foo bar acitve"></p>
```

### 绑定内联样式

* 对象语法`v-bind:style`
1. 基本例子
```
<div v-bind:style="{color:activeColor, fontSize: fontSize + 'px'}"></div>

data: {
    activeColor: "#fff",
    fontSize: 12
}

// 渲染结果
<div style="color:#fff;font-size:12px"></div>
```
2. 直接使用对象（也可以使用计算属性）
```
<div v-bind:style="styleObject"></div>

data: {
    styleObject: {
        color: "#fff",
        fontSize: "12px"
    }
}

// 渲染结果
<div style="color:#fff;font-size:12px"></div>
```

* 数组语法
```
<div v-bind:style="[baseStyles, overrideStyles]"></div>

data: {
    baseStyles: {
        color: "red",
        fontSize: "10px"
    },
    overrideStyles: {
        color: "#fff",
        fontSize: "12px"
    }
}

// 渲染结果
<div style="color:#fff;font-size:12px"></div>
```

* 自动添加前缀

当`v-bind:style`需要添加浏览器引擎前缀的CSS属性时，如`-webkit-transform`等，浏览器会自动侦测并添加相应的前缀

* 多重值

> 2.3.0+

从2.3.0版本起，可以为`style`绑定的中的属性提供一个包含多个值的数组，常用于提供多个带前缀的值：
```
<div :style="{display: ['-webkit-box', '-ms-flexbox', 'flex']}"></div>
```

浏览器会渲染数组中最后一个被支持的值。如果浏览器支持不带前缀，则会自动选择无前缀的渲染，本例最终为`display:flex`

## 条件渲染

### `v-if`

```
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>

data: {
    ok: false
}

// 渲染结果
<h1>No</h1>
```

* 在`<template>`元素上使用`v-if`

如果想要切换一组元素则可使用`<template>`，他是不可见的（不会被渲染出来）

```
<template v-if="ok">
    <h1>Title</h1>
    <p>Paragram 1</p>
    <p>Paragram 2</p>
</template>
```

* `v-else` & `v-else-if`

```
<div v-if="type === 'A'">
    A
</div>
<div v-else-if="type === 'B'">
    B
</div>
<div v-else-if="type === 'C'">
    C
</div>
<div else-if>
    Not A/B/C
</div>

data: {
    type: 'D'
}

// 渲染结果
<div>Not A/B/C</div>
```

* 用`key`管理可复用元素

```
<template v-if="loginType === 'username'">
    <label>Username</label>
    <input placeholder="Enter your username" key="username-input" />
</template>
<template v-else>
    <label>Email</label>
    <input placeholder="Enter your email address" key="email-input" />
</template>

<button @click="toggle">Toggle login type</button>

data: {
    loginType: 'username'
}
methods: {
    toggle: function() {
        this.loginType = this.loginType === 'username' ? 'email' : 'username';
    }
}
```

如上两个模板，没有`key`的元素会被复用，如`label`，而`input`设置了`key`值所以每次都会重新渲染（即内容会被清除）

### `v-show`

```
<h1 v-show="ok">Hello</h1>

data: {
    ok: true
}

// 渲染结果
<h1>Hello</h1>
```

带有`v-show`的元素始终被保存在DOM，只是对元素的CSS属性`display`的简单切换

> `v-show`不支持`<template>`也不支持`v-else`

### `v-if` VS `v-show`

* `v-if`是真正的条件渲染，会确保条件块儿中的**事件监听**和**子组件**被销毁和重建
* `v-if`惰性的，如果条件为假，则块儿代码不做任何操作，只有为真时才开始渲染
* `v-show`总是会渲染，不管初始条件是什么，因为只是简单的基于`CSS`切换
* `v-if`切换的开销大于`v-show`，如果频繁切换则建议使用`v-show`；如果在运行时改变较少，则使用`v-if`

> `v-for`优先级高于`v-if`

## 列表渲染

### 用`v-for`把一个数组对应为一组元素

### 一个对象的`v-for`

### 数组更新检测

### 











