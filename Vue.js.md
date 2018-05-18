# Learning Vue.js
> 参考 https://cn.vuejs.org/v2/guide/

> Last edit at May 1st. Start at April 27th.

## Installation

* \<script\>直接引入 (<https://cdn.jsdelivr.net/npm/vue>)

    <https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js>

* npm安装, 以配合webpack或者browsify模块打包器
```
    npm i vue
```
* CLI
```
    npm i -g vue-cli
    vue init webpack v-myproject
    cd v-myproject
    npm run dev
```
* 其他
  - 完整版, 包括编译器(编译模板)和运行时版本
  - UMD (浏览器), CommonJS (Browserify & Webpack1), ES Module (webpack2 & Rollup)
  - 运行时版本比完整版小约30%
  - 对于CommonJS和ES Module版本, 需要自己压缩发布
  - UMD版本可以直接用于AMD模块加载器 (Require.js)
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
* 数据和DOM已经建立了连接
* app.message改变将触发UI上的更新

```
    <div id="app">
        <span v-bind:title="message">鼠标悬停查看提示信息</span>
    </div>
```
* v-bind称为指令 (指令都带有前缀v-)

### 条件渲染
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
* app.seen的改变引起DOM结构变化 (元素显示隐藏)
* 可以用到过渡&动画效果
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
* v-for, 渲染一个列表

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
* v-model实现表单输入框和数据的双向绑定

### 组件化构建
* 在Vue中, 一个组件就是一个预定义的Vue实例
```
    Vue.component("todo-item", {
        props: [ "todo" ],
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
* props: [ "todo" ], 当前组件接收一个名为"todo"的属性
* v-bind:todo="item", 为当前组件创建一个名为"todo"的属性，并绑定item

## Vue实例
### 创建一个Vue实例, 下面是完整的参数列表
```
    let vm = new Vue({ // 参数 });
```
* 数据: data, props, propsData, computed, methods, watch
* DOM: el, template, render, renderError
* 生命周期钩子: beforeCreate, created, beforeMount, mounted, beforeUpdate, updated, activated, deactivated, beforeDestroy, destroyed, errorCaptured
* 资源: directives, filters, components
* 组合: parent, mixins, extends, provide/inject
* 其他: name, delimeters, functional, model, inheritAttrs, comments

### 数据与方法

data对象中的属性, 就是Vue实例的属性!!!
```
    let data = { a: 1 };
    let vm = new Vue({ el: "#app", data: data });
    
    vm.a === data.a;
    vm.a = 10; // data.a === 10
    data.a = 20; // vm.a === 20
```
* 这些数据的改变, 会引起绑定的视图的重新渲染
* Object.freeze(data); 整个响应系统将不会工作(变化不会引起更新)
* 实例属性和方法, 前缀加$, 其后是
```
    vm.$data === data;
    vm.$el === document.getElementById("app");
    vm.$watch("a", function(newVal, oldVal) {
        // 当data.a改变之后被调用执行
    });
```
* 实例属性和方法的完整列表
  * 属性: $data, $props, $el, $options, $parent, $root, $children, $slots, $scopedSlots, $refs, $isServer, $attrs, $listeners
  * 方法/数据: $watch, $set, $delete
  * 方法/事件: $on, $once, $off, $emit
  * 方法/生命周期: $mount, $forceUpdate, $nextTick, $destroy

### 生命周期钩子
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
  * beforeCreate (实例化之后, data observer & event/watcher配置之前)
  * created (挂载开始之前)
  * beforeMount (render首次调用)
  * mounted (el被vm.$el替换, DOM渲染完成? DOM和实例建立连接?)
  * beforeDestroy, destroyed (实例销毁, 实例和所有东西解除绑定, 移除事件监听, 所有子实例被销毁)
  * beforeUpdate (数据更新, 虚拟DOM修改之前)
  * updated (数据更新并导致DOM重绘之后)
  * activated, deactivated
  * errorCaptured (来自子孙组件的错误, 返回false阻止向上传播)

## 模板语法
* Vue使用了基于HTML的模板语法, 声明式语法将DOM和Vue实例数据绑定
* Vue将模板编译成虚拟DOM渲染函数, 结合相应式系统, Vue能够计算出最少需要重新渲染多少组件, 并把DOM操作次数剪到最少
* 可以实现render函数, 使用JSX语法 (Babel插件)

### 插值 (Mustache语法, 双大括号)
* 文本
```
    <span>Message: {{ msg }}</span>
    <span v-once>只能执行一次插值: {{ msg }}</span>
```
* 原始HTML, 动态渲染任意HTMl非常危险, 很容易导致XSS攻击
```
    let data = { rawHtml: "<span style='color:red'>Red</span>" };
    // ...

    <p>{{ rawHtml }}</p>    // <p><span...></p>
    <p v-html="rawHtml"></p>    // <p>Red</p>
```
* 属性 (HTML原生属性)
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

### 指令 (Directives, 带有v-前缀的特殊属性, 是Vue特有的)
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
* 缩写 (v-bind和v-on会被大量使用, 故提供特定简写)
```
    v-bind:id  === :id
    v-on:click === @click
```

















