# My-ECMAScript-6
> Simple overview of ES6 (ES2015), see full specification at [ECMAScript standard](http://www.ecma-international.org/ecma-262/6.0/). This article is based on <http://es6-features.org>

## Useful Resources
* [Learn ES2015 in Babel](http://babeljs.io/learn-es2015)
* [lukehoban/es6features](https://github.com/lukehoban/es6features)
* [阮一峰 ES6 入门](http://es6.ruanyifeng.com/)
* [compat table](http://kangax.github.io/compat-table/es6/)
* [Can I use](https://caniuse.com)

## Overview (New features in ES6)
* Constants. 常量. [Goto](#Constants)
* Scoping. 作用域. [Goto](#Scoping)
* Arrow Functions. 箭头函数. [Goto](#Arrow-Functions)
* Extended Parameter Handling. 扩展运算符 [Goto](#Extended-Parameter-Handling)
* Template Literals. 模板字面量. [Goto](#Template-Literals)
* Extended Literals. 增强的字面量. [Goto](#Extended-Literals)
* Enhanced Regular Expression. 增强的正则表达式. [Goto](#Enhanced-Regular-Expression)
* Enhanced Object Properties. 增强的对象属性. [Goto](#Enhanced-Object-Properties)
* Destructuring Assignment. 赋值解构. [Goto](#Destructing-Assignment)
* Modules. 模块. [Goto](#Modules)
* Classes. 类. [Goto](#Classes)
* Symbol Type. Symbol. [Goto](#Symbol-Type)
* Iterators. 迭代. [Goto](#Iterators)
* Generators. 生成器. [Goto](#Generators)
* Map/Set & WeakMap/WeakSet. 集合. [Goto](#Map/Set-&-WeakMap/WeakSet)
* Typed Arrays. 类型数组. [Goto](#Typed-Arrays)
* New Built-In Methods. 新的内置API方法. [Goto](#New-Built-In-Methods)
* Promises. Promise. [Goto](#Promises)
* Meta-Programming. 反射. [Goto](#Meta-Programming)
* Internationalization & Localization. 国际化 & 本地化. [Goto](#Internationalization-&-Localization)

### Constants
Immutable variables. variable itself cannot be re-assigned (But object content can still be altered).
```js
  // ES6
  const PI = 3.141593;

  // ES5
  Object.defineProperty(typeof global === "object" ? global : window, "PI", {
    value: 3.141593,
    enumerable: true,
    writable: false,
    configurable: false
  });
```

### Scoping, Block-Scope is ES6 {} and IIFE (Immediately-Invoked Function Expression)
* Block-scoped variables
```js
    // ES6
    let callbacks = [];
    for (let i = 0; i < 10; i += 1) {
        callbacks[i] = function() { return i * 2; };
    }
    callbacks[0](); // 0
    callbacks[1](); // 2
    callbacks[2](); // 4

    // ES5
    var callbacks = [];
    for (var i = 0; i < 10; i += 1) {
        (function(index) {
            callbacks[index] = function() { return index * 2; };
        })(i);
    }
    callbacks[0](); // 0
    callbacks[1](); // 2
    callbacks[2](); // 4
```
* Block-scoped functions
```js
    // ES6
    {
        function foo() { return 1 }
        foo(); // 1
        {
            function foo() { return 2 }
            foo(); // 2
        }
        foo(); // 1
    }
    
    // ES5
    (function() {
        function foo() { return 1 };
        foo(); // 1
        (function() {
            function foo() { return 2 };
            foo(); // 2
        })();
        foo(); // 1
    })();
```

### Arrow Functions
* Expression & Statement
```javascript
    // ES6
    let evens = [0, 2, 4, 6, 8];
    let odds  = evens.map(v => v + 1);
    let pairs = evens.map(v => ({ even: v, odd: v + 1 }));
    let nums  = evens.map((v, i) => v + i);
    evens.forEach(v => {
        if (v % 3 === 0) {
            array.push(v);
        }
    });
    
    // ES5
    let odds = evens.map(function(v) { return v + 1; });
    let paris = evens.map(function(v) { return { even: v, odd: v + 1 }; });
    let nums = evens.map(function(v, i) { return v + i; });
    evens.forEach(function(v) {
        if (v % 3 === 0) {
            array.push(v);
        }
    });
```
* Lexical this, more intuitive handling of current object context
```javascript
    // ES6
    let oInst = {
        value: 10,
        print: function() {
            setTimeout(() => {
                console.log(this.value);
            }, 1000);
        }
    };
    oInst.print(); // 10
    
    // ES5
    var oInst = {
        value: 10,
        print: function() {
            // Solution 1
            var that = this;
            setTimeout(function() {
                console.log(that.value);
            }, 1000);
            
            // Solution 2
            setTimeout(function() {
                console.log(this.value);
            }.bind(this), 1000);
        }
    };
    oInst.print(); // 10 10
```

### Extended Parameter Handling
* Default Parameter Values. Simple and intuitive(直观的) default values for function parameters.
```js
    // ES6
    function f(x, y = 7) {
        return x + y;
    }
    f(1); // 8
    
    // ES5
    function f(x, y) {
        if (y === undefined) {
             y = 7;
        }
        return x + y;
    }
    f(1); // 8
```
* Rest Parameter (扩展运算符). Aggregation of remaining arguments into single parameter of variable functions
```javascript
    // ES6
    function f(x, ...a) {
        return x + a.length;
    }
    f(1, 2, 3, 4); // 4
    
    // ES5
    function f(x) {
        var a = Array.prototype.slice.call(arguments, 1);
        return x + a.length;
    }
    f(1, 2, 3, 4); // 4
```
* Spread Operator, spreading of elements of an array|string into literal elements and individual function parameters
```javascript
    // ES6
    let values = ["a", "b"];
    let array = [1, 2, ...values]; // 1, 2, "a", "b"

    function f(x, ...y) {
        return x + y.length;
    }
    f(1, ...array); // 5
    
    [ ..."abc" ]; // [ "a", "b", "c" ]

    // ES5
    var values = ["a", "b"];
    var array = [1, 2].concat(values); // 1, 2, "a", "b"

    function f(x) {
        return x + (arguments.length - 1);
    }
    f.apply(null, [1].concat(array)); // 5

    "abc".split(""); // [ "a", "b", "c" ]
```

### Template Literals
* String interpolation, intuitive expression interpolation for single-line or multi-line strings. (In draft spec this is called "Template Strings")
```javascript
    // ES6
    let o = { name: "Tom", age: 5 };
    let message = `That is ${o.name},
        he is ${o.age} years old.`;
    
    // ES5
    var o = { name: "Tom", age: 5 };
    var message = "That is " + o.name + ",\n" + 
        "he is " + o.age + "years old.";
```
* Custom interpolation, flexible expression interpolation for arbitrary(任意的) methods
```javascript
    var o = { name: "Tom", age: 5 };

    // ES6
    fn`That is ${o.name} and he is ${o.age} years old.`;

    // ES5
    fn([ "That is ", " and he is ", " years old."], o.name, o.age);
```
* Raw String Access (String.raw only work in template string!!!)
```javascript
    function f(strings, ...values) {
        strings[0] === "He is \n ";
        strings[1] === " years old.";
        strings[0].raw[0] = "He is \\n ";
        strings[1].raw[1] = " years old.";
        values[0] === 42;
    }
    f `He is \n ${ 42 } years old.`;

    String raw `He is \n ${ 42 } years old.` === "He is \\n 42 years old.";
```

### Extended Literals
> Binary & Octal Literal
* Direct support for safe binary and octal literals
```javascript
    // ES6
    0b1101 === 13;
    0o15 === 13;

    // ES5
    parseInt("1101", 2) === 13;
    parseInt("15", 8) === 13;
    015 === 13; // non-strict mode
```
* Unicode String & RegExp Literal, Extended support using Unicode within strings and regular expressions
```javascript
    "𠮷".length === 2;
    "𠮷".match(/./u)[0].length === 2;
    "𠮷" === "\uD842\uDF87";
    "𠮷" === "\u{20BB7}";
    "𠮷".codePointAt(0) === 0x20BB7;
    // "𠮷".codePointAt(0) -> 134071
    // (134071).toString(16) -> 20BB7
    for (let code of "𠮷") {
        console.log(code);
    }   // "𠮷"
```

### Enhanced Regular Expression
> Regular Expression Sticky Matching, refer to [Detail](http://es6-features.org/#RegularExpressionStickyMatching)

### Enhanced Object Properties
* Property Shorthand, Shorter syntax for common object property definition idiom.
```javascript
    // ES6
    let x = 0, y = 0;
    let obj = { x, y }; // { x: 0, y: 0 }

    // ES5
    var x = 0, y = 0;
    var obj = { x: x, y: y }; // { x: 0, y: 0 }
```

* Computed Property Names, Support for computed names in object property definitions.
```javascript
    // ES6
    function f() {
        return "A";
    }
    let obj = {
        [ "key" + f() ]: "value"
    };

    // ES5
    var obj = {};
    obj[ "key" + f() ] = "value";
```

* Method Properties, Support for method notation in object property definitions, for both regular functions and generator functions
```javascript
    // ES6
    obj = {
        fn() {
            // Do sth
        }
    };

    // ES5
    obj = {
        fn: function() {
            // Do sth
        }
    };
```

### Destructing Assignment
* Array Mathcing, Instuitive(任意的) and flexible destructing of Arrays into individual variables during assignment
```js
    // ES6
    let values = [ 1, 2, 3 ];
    let [ a, , b ] = values;    // a == 1, b == 3
    [ a, b ] = [ b, a ];    // a == 3, b == 1

    // ES5
    var values = [ 1, 2, 3 ];
    var a = values[0], b = values[2];
    var tmp = a; a = b; b = a;
```

* Object matching, Shorthand Notation, Intuitive and flexible destructing of Objects into individual variables during assignment
```js
    var obj = { x: 1, y: 2, z: 3 };

    // ES6
    let { x, y, z } = obj;
    
    // ES5
    var x = obj.x,
        y = obj.y,
        z = obj.z;
```

* Object matching, Deep matching, Intuitive and flexiable destructing of Objects into individual variables during assignment.
```js
    var obj = { x: 1, y: { y1: 2 }, z: 3 };

    // ES6
    let { x: a, y: { y1: b }, z: c } = obj;

    // ES5
    var a = obj.x,
        b = obj.y.y1,
        c = obj.z;
```

* Object And Array Matching, Default Values, Simple and Intuitive default values for destructing of Objects and Arrays
```js
    var obj = { a: 1 };
    var arr = [ 1 ];

    // ES6
    let { a, b = 2 } = obj; // a === 1, b === 2
    let [ x, y = 2 ] = arr; // x === 1, y === 2

    // ES5
    var a = obj.a,
        b = obj.b === undefined ? 2 : obj.b,
        x = arr[0],
        y = arr[1] === undefined ? 2 : arr[1];
```

* Parameter Context Matching, Intuitive and flexiable destructing of Arrays and Objects into individual parameters during function calls.
```js
    // ES6
    function f([ name, val ]) {
        console.log(name, val);
    }
    function g({ name: n, val: v }) {
        console.log(n, v);
    }
    function h({ name, val }) {
        console.log(name, val);
    }

    f([ "bar", "foo" ]); // "bar" "foo"
    g({ name: "bar", val: "foo" }); // "bar" "foo"
    h({ name: "Tom", val: "Jerry" }); // "Tom" "Jerry"

    // ES5
    function f(args) {
        console.log(args[0], args[1]);
    }
    function g(arg) {
        console.log(arg.name, arg.val);
    }
    function h(arg) {
        console.log(arg.name, arg.val);
    }
    f([ "bar", "foo" ]); // "bar" "foo"
    g({ name: "bar", val: "foo" }); // "bar" "foo"
    h({ name: "Tom", val: "Jerry" }); // "Tom" "Jerry"
```

* Fail-Soft(失效) Destructing, optional with defaults
```js
    var arr = [ 11, 12 ];

    // ES6
    let [ a = 1, b = 2, c = 3, d ] = arr;
    a === 11;
    b === 12;
    c === 3;
    d === undefined;

    // ES5
    var a = arr[0] !== undefined ? arr[0] : 1;
    var b = arr[1] !== undefined ? arr[1] : 2;
    var c = arr[2] !== undefined ? arr[2] : 3;
    var d = arr[3] !== undefined ? arr[3] : undefined;
    a === 11;
    b === 12;
    c === 3;
    d === undefined;
```

### Modules
* Value Export/Import, Support for exporting/importing values from/to modules without global namespace pollution.
```js
    // ES6
    // lib/math.js
    export function sum(x, y) { return x + y };
    export var PI = 3.1415;

    // app.js
    import * as math from "lib/math";
    import { sum, PI } from "lib/math";

    math.sum === sum;   // true
    math.PI === PI;     // true

    // ES5
    // lib/math.js
    libMath = {};
    libMath.sum = function(x, y) { return x + y };
    libMath.PI = 3.1415;
    window.math = libMath;

    // app.js
    var fnSum = math.sum,
        PI = math.PI;
    
    fnSum == math.sum;
    PI = math.PI;
```

* Default & wildcard(通配符)
> Marking a value as the default exported value and  mass-mixin of values.
```js
    // ES6
    // lib/math2.js
    export * from "math";
    export let E = 2.718;
    export default (x) => Math.exp(x);

    // app.js
    import exp, { PI, E } from "lib/math2";
    console.log(PI, E, exp(0)); // 3.1415, 2.718, 1

    // ES5
    // lib/math2.js
    libMath2 = {};
    for (var s in libMath) {
        libMath2[s] = libMath[s];
    }
    libMath2.E = 2.718;
    libMath2.exp = function(x) {
        return Math.exp(x);
    }
    window.math2 = libMath2;

    // app.js
    let exp = math2.exp,
        PI = math2.PI,
        E = math2.E;
    console.log(PI, E, exp(0)); // 3.1415, 2.718, 1
```

### Class
* Class Definition. More Intuitive, OOP-style and boilerplate-free(无样板) classes.
```js
    // ES6
    class Shape {
        consturctor(id, x, y) {
            this.id = id;
            this.move(x, y);
        }
        move(x, y) {
            this.x = x;
            this.y = y;
        }
    }

    // ES5
    function Shape(id, x, y) {
        this.id = id;
        this.move(x, y);
    }
    Shape.prototype.move = function(x, y) {
        this.x = x;
        this.y = y;
    }
```

* Class Inheritance
> More intuitive, OOP-style and boilerplate-free inheritance
```js
    // ES6
    class Circle extends Shape {
        constructor(id, x, y, radius) {
            super(id, x, y);
            this.radius = radius;
        }
    }

    // ES5
    function Circle(id, x, y, radius) {
        Shape.call(this, id, x, y);
        this.radius = radius;
    }
    Circlr.prototype = Object.create(Shape.prototype);
    Circle.prototype.constructor = Circle;
```

* Class Inheritance, from Expressions
> Support for mixin-style inheritance...
```js
    // Nothing
```

* Base Class Access
> Intuitive access to base class constructor and methods (super)
```js
    // ES6
    class Circle extends Shape {
        ...
        draw() {
            super.move(1, 2);
        }
    }

    // ES5
    function Circle() {
        ...
    }
    Circle.prototype.draw = function() {
        Shape.prototype.move.call(this, 1, 2);
    }
```

* Static Members
> Simple support for static class members.
```js
    // ES6
    class Shape {
        static fill(val) {
            console.log(val);
        }
    }
    Shape.fill("static fn fill"); // "static fn fill"

    // ES5
    function Shape() {
        ...
    }
    Shape.fill = function(val) {
        console.log(val);
    }
    Shape.fill("static fn fill"); // "static fn fill"
```

* Getter & Setter
> Getter/Setter also directly within classes (and not just within object initializers, as it is possible since ECMASciprt 5.1).
```js
    // ES6
    // DO NOT GET/SET this.radius in "set radius(r)" & "get radius()", or it will drop in infinite looping
    class Circle {
        constructor(radius) {
            this._radius = radius;
        }
        set radius(r) { this._radius = r }
        get radius()  { return this._radius; }
    }
    let c = new Circle(10);
    c.radius; // 10

    // ES5
    function Circle(r) {
        this._radius = r;
    }
    Circle.prototype = {
        set radius(r) { this._radius = r },
        get radius()  { return this._radius; }
    };
    var c = new Circle(2);
    c.radius; // 2
```

### Symbol Type
* Symbole Type. Unique and immutable(不可变的) data type to be used and identifier for object properties. Symbol can have and optional description, but for debugging purposes only.
```js
    // ES6
    Symbol("foo") !== Symbol("foo");    // true
    const bar = Symbol();
    typeof bar === "symbol";

    let obj = {};
    obj[bar] = "bar";
    JSON.stringify(obj); // {}
    Object.keys(obj); // []
    Object.getOwnPropertyNames(obj); // []
    Object.getOwnPropertySymbols(obj); // [ "bar" ]
```

* Global Symbols
> Global Symbols, indexed through unique keys.
```js
    // ES6
    Symbol.for("foo") === Symbol.for("foo"); // true
    const bar1 = Symbol.for("bar1");
    const bar2 = Symbol.for("bar2");
    Symbol.keyFor(bar1) === "bar1"; // true
    Symbol.keyFor(bar2) === "bar2"; // true
    typeof bar1 === "symbol"; // true
    typeof bar2 === "symbol"; // true

    let obj = {};
    obj[bar1] = "bar";
    JSON.stringify(obj); // {}
    Object.keys(obj); // []
    Object.getOwnPropertyNames(obj); // []
    Object.getOwnPropertySymbols(obj); // [ "bar" ]
```
### Iterators
* Iterator & For-of Operator
> Support "iterable" protocol to allow objects to customize their  iteration behavior. Additionally, support "iterator" protocol produce sequence of values (either finite or infinite). Finally, provide convenient of operator iterate over all values of an iterable object.
```js
    // ES6
    let fibonacci = {
        [Symbol.iterator]() {
            let pre = 0, cur = 1;
            return {
                next() {
                    [ pre, cur ] = [ cur, pre + cur ];
                    return { done: false, value: cur };
                }
            }
        }
    }
    for (let n of fibonacci) {
        if (n > 1000) {
            break;
        }
        console.log(n);
    }

    // ES5
    var fibonacci = {
        next: (function() {
            var pre = 0, cur = 1;
            return function() {
                var tmp = pre;
                pre = cur;
                cur += tmp;
                return cur;
            }
        })()
    };
    var n;
    for (;;) {
        n = fibonacci.next();
        if (n > 1000) {
            break;
        }
        console.log(n);
    }
```

### Generators
> Since async/await are standardized in ES2017, and it's more friendly to write asynchronized codes. Refer to [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function).

### Map/Set & WeakMap/WeakSet
* Set Data-Structure. Cleaner data-structure for common algorithms based on sets
```js
    // ES6
    let s = new Set();
    s.add("hello").add("world");
    s.size === 2; // true
    s.has("hello"); // true
    for (let k of s) {
        console.log(k);
    }
    // "hello" "world"

    // ES5
    var s = {};
    s["hello"] = true;
    s["world"] = true;
    Object.keys(s).length === 2; // true
    s["hello"] === true; // true
    for(var k in s) {   // arbitrary order 任意顺序
        if (s.hasOwnProperty(k)) {
            console.log(s[k]);
        }
    }
    // true true
```

* Map Data-Structure. Cleaner data-structure for common algorithms based on maps.
```js
    // ES6
    let m = new Map();
    let s = Symbol();
    m.set("hello", 10);
    m.set(s, 11);
    m.get("hello") === 10; // true
    m.get(s) === 11; // true
    m.size === 2; // true
    for (let [ k, v ] of m.entries()) {
        console.log(k + " = " + v);
    }
    // "hello" = 10
    // TypeError: Cannot convert a Symbol value to a string

    // ES5
    var m = {};
    m["hello"] = 10;
    Object.keys(m).length === 1; // true
    for (var key in m) {
        if (m.hasOwnProperty(key)) {
            console.log(key + " = " + m[key]);
        }
    }
    // "hello" = 10
```

* Weak-Link Data-Structure. Memory-leak-free Object-key'd side-by-side data-structure
```js
    // ES6
    let isMarked = new WeakSet();
    let attachedData = new WeakMap();

    class Node {
        constructor(id) { this.id = id }
        mark() { isMarked.add(this) }
        unmark() { isMarked.delete(this) }
        marked() { return isMarked.has(this) }
        set data (data) { attachedData.set(this, data) }
        get data () { return attachedData.get(this) }
    }
    let node = new Node("foo");
    JSON.stringify(node); // '{"id":"foo"}'
    node.mark();
    node.data = "bar";
    node.data === "bar"; // true
    JSON.stringify(node); // '{"id":"foo"}'
    
    isMarked.has(node) === true; // true
    attachedData.has(node) === true; // true

    node = null; // empty reference
    attachedData.has(node) === false; // true
    isMarked.has(node) === false; // true
```

### Typed Arrays
* Typed Arrays. Support for arbitary byte-based data structures to implement network protocols, cryptography algorithms, file format manipulation, etc.

Type | Value Range
---- | -----------
Int8Array | -128 ~ 127
Uint8Array | 0 ~ 255
Uint8ComparedArray | 0 ~ 255
Int16Array | -32768 ~ 32767
Uint16Array | 0 ~ 65545
Int32Array | -2^31 ~ 2^31-1
Uint32Array | 0 ~ 2^32-1
Float32Array | 1.2x10^-38 ~ 3.4x10^38
Float64Array | 5.0x10^-324 ~ 1.8x10^308

> **ArrayBuffer** is a data container, can be accessed by TypedArray(ArrayBuffer, [byteOffset, length]) and DataView(ArrayBuffer, [byteOffset, length])

> **DataView** is another way to set/get ArrayBuffer, such as set/getInt8, set/getUint16 etc.

```js
    // ES6
    class Example {
        // 24 byte length
        constructor(buffer = new ArrayBuffer(24)) {
            this.buffer = buffer;
        }
        set buffer(buffer) {
            this._buffer = buffer;
            // 4 bytes (32 bits per unit)
            this._id = new Uint32Array(this.buffer, 0, 1);
            // 16 bytes (8 bits per unit)
            this._username = new Uint8Array(this._buffer, 4, 16);
            // 4 bytes (32 bites per unit)
            this._amountDue = new Float32Array(this._buffer, 20, 1);
        }
        get buffer() { return this._buffer }
        set id(v) { this._id[0] = v }
        get id() { return this._id[0] }
        set username(v) { this._username[0] }
        get username() { return this._username[0] }
        set amountDue(v) { this._amountDue[0] = v }
        get amountDue() { return this._amountDue }
    }
    let exmaple = new Example();
    example.id = 7;
    example.username = "Tom";
    example.amountDue = "30";
```

### New built-in Methods
* Object Property Assignment. New Function for assigning enumerable properties of one or more source objects onto a destination object.
```js
    // ES6
    let dest = { a: 0 };
    let src1 = { b: 1 };
    let src2 = { b: 2 };
    Object.assign(dest, src1, src2);
    console.log(dest); // { a: 0, b: 2 }

    // ES5
    var dest = { a: 0 };
    var src1 = { b: 1 };
    var src2 = { b: 2 };
    Object.keys(src1).forEach(function(k) {
        dest[k] = src1[k];
    });
    Object.keys(src2).forEach(function(k) {
        dest[k] = src2[k];
    });
    console.log(dest); // { a: 0, b: 2 }
```

* Array Element Finding. New function for finding an element in an array
```js
    // ES6
    [ 1, 3, 4, 2 ].find(x => x > 3); // 4
    [ 1, 3, 4, 2 ].findIndex(x => x > 3); // 2

    // ES5
    [ 1, 3, 4, 2 ].filter(function(x) {
        return x > 3;
    })[0];
```

* String Searching & Repeating. New string repeating functionalities and search for sub-string.
```js
    // ES6
    "-".repeat(4); // ----
    "hello".startsWith("ello", 1); // true, 1 is start index
    "hello".endsWith("hell", 4); // true, 4 is end index
    "hello".includes("ell"); // true
    "hello".includes("ell", 2); // false, 2 is start index

    // ES5
    Array(4 + 1).join("-"); // ----
    "hello".indexOf("ello") === 1; // true
    "hello".indexOf("hell") === (4 - "hello".length); // true
    "hello".indexOf("ell") !== -1; // true
    "hello".indexOf("ell", 2) !== -1; // false
```

* Number Type Checking. New functions for checking for non-numbers and finite numbers.
```js
    // ES6
    Number.isNaN(30); // false
    Number.isNaN(NaN); // true
    Number.isFinite(Infinity); // false
    Number.isFinite(-Infinity); // false
    Number.isFinite(NaN); // false
    Number.isFinite(123); // true

    // ES5
    function isNaN(n) { return n !== n; }
    function isFinite(v) {
        return (typeof v === "number"
            && !isNaN(v)
            && v !== Infinite
            && v !== -Infinite);
    }
    isNaN(30); // false
    isNaN(NaN); // true
    isFinite(Infinite); // false
    isFinite(-Infinite); // false
    isFinite(NaN); // false
    isFinite(123); // true
```

* Number Safety checking. Checking whether an integer number is in the safe range, i.e., It is correctly represented by Javascript (Where all numbers, including integer numbers, are technically floating point number).
```js
    // ES6
    Number.isSafeInteger(30); // true
    Number.isSafeInteger(9007199254740992); // false, 2^53

    // ES5
    function isSafeInteger(n) {
        return (typeof n === "number
            && Math.round(n) === n
            && -(Math.pow(2, 53) - 1) <= n
            && n <= (Math.pow(2, 53) - 1);
        );
    }
    isSafeInteger(30); // true
    isSafeInteger(9007199254740992); // false
```

* Number Comparison. Availability of a standard Epsilon value for more comparison of floating point numbers.
```js
    // ES6
    (0.1 + 0.2) === 0.3; // false
    Math.abs((0.1 + 0.2) - 0.3) < Number.EPSILON; // true

    // ES5
    (0.1 + 0.2) === 0.3; // false
    Math.abs((0.1 + 0.2) - 0.3) < 2.220446049250313e-16; // true
```

* Number Trunction. Truncate a floating point number to its integral part, completely dropping the fractional part.
```js
    // ES6
    Math.truncate(42.7); // 42
    Math.truncate(0.1); // 0
    Math.truncate(-0.1); // -0

    // ES5
    function mathTrunc(x) {
        return (x < 0 ? Math.ceil(x) : Math.floor(x));
    }
    mathTrunc(42.7); // 42
    mathTrunc(0.1); // 0
    mathTrunc(-0.1); // -0
```

* Number Sign Determination. Determine the sign of a number, including special cases of signed zero and non-number.
```js
    // ES6
    Math.sign(7); // 1
    Math.sign(0); // 0
    Math.sign(-0); // -0
    Math.sign(-7); // -1
    Math.sign(NaN); // NaN

    // ES5
    function mathSign(x) {
        return ((x === 0 || isNaN(x)) ? x : (x > 0 ? 1 : -1));
    }
    mathSign(7); // 1
    mathSign(0); // 0
    mathSign(-0); // -0
    mathSign(-7); // -1
    mathSign(NaN); // NaN
```

### Promises
* Promise Usage. First class reprentation of a value that may be made asynchrously and be available in the future.

State | Description
----- | -----------
pending | initial state, neither fulfilled or rejected
fulfilled | operation completed successfully
rejected | operation failed

Static Methods | Description
------- | -----------
Promise.all | Promise list that all are resolved
Promise.race | Promise list once one is resolved
Promise.reject | Directly reject
Promise.resolve | Directly resolve

Methods | Description
------- | -----------
then    | then(fnResolve, fnReject)
catch   | catch(fnReject) === then(undefined, fnReject)
finally | finally(fnFinally)

```js
    // ES6
    function msgAfterTimeout(msg, who, timeout) {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                resolve(`${msg} Hello ${who}!`);
            }, timeout);
        });
    }
    msgAfterTimeout("", "Foo", 100)
        .then(msg => {
            return msgAfterTimeout(msg, "Bar", 200);
        })
        .then(msg => {
            console.log(`done after 300ms: ${msg}`);
        });
    
    // ES5
    function msgAfterTimeout(msg, who, timeout, onDone) {
        setTimeout(function() {
            onDone(msg + " Hello " + who + "!");
        }, timeout);
    }
    msgAfterTimeout("", "Foo", 100, function(msg) {
        msgAfterTimeout(msg, "Bar", 200, function(msg) {
            console.log("done after 300ms: " + msg);
        });
    });
```

### Meta-Programming
* Proxying. Hooking into runtime-level object meta-operations.
```js
    let target = { foo: "Welcome, foo" };
    let proxy = new Proxy(target, {
        get(receiver, name) {
            // receiver === target
            // name === proxy.xxx
            return (name in receiver) ? receiver[name] : `Hello, ${name}`;
        }
    });
    proxy.foo; // "Welcome, foo"
    proxy.world; // "Hello, world"
```
* Reflection. Make calls corresponding to the object meta-operations.
```js
    // ES6
    let obj = { a: 1 };
    Object.defineProperty(obj, "b", { value: 2});
    obj[Symbol("c")] = 3;
    Reflection.ownKeys(obj); // [ "a", "b", Symbol(c) ]

    // ES5
    var obj = { a: 1 };
    Object.defineProperty(obj, "b", { value: 2 });
    Object.getOwnPropertyNames(obj); // [ "a", "b" ]
```

### Internationalization & Localization
* Number Formatting
```js
    let l10nEN = new Intl.NumberFormat("en-US");
    let l10nDE = new Intl.NumberFormat("de-DE");
    l10nEN.format(1234567.89); // "1,234,567.89"
    l10nDE.format(1234567.89); // "1.234.567.89"
```

* Currency Formatting. Format numbers with digit grouping, localized separators and attached currency symbol.
```js
    let l10nUSD = new Intl.NumberFormat("en-US", { style: "CURRENCY", currency: "USD" });
    let l10nGBP = new Intl.NumberFormat("en-GB", { style: "CURRENCY", currency: "GBP" });
    let l10nEUR = new Intl.NumberFormat("de-DE", { style: "CURRENCY", currency: "EUR" });
    l10nUSD.format(100200300.40); // "$100,200,300.40"
    l10nGBP.format(100200300.40); // "£100,200,300.40"
    l10nEUR.format(100200300.40); // "100.200.300,40 €"
```

* Date/Time Formatting. Format date/time with localized ordering and separators.
```js
    let l10nEN = new Intl.DateTimeFormat("en-US");
    let l10nDE = new Intl.DateTimeFormat("de-DE");
    l10nEN.format(new Date("2018-05-02")); // "5/2/2018"
    l10nDE.format(new Date("2018-05-02")); // "2.5.2018"
```
