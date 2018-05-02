# My-ECMAScript-6
Journey to ES-6, see full specification at [ES6 standard](http://www.ecma-international.org/ecma-262/6.0/). This article is based on <http://es6-features.org>

## Learning resources
* [Learn ES2015 in Babel](http://babeljs.io/learn-es2015)
* [lukehoban/es6features](https://github.com/lukehoban/es6features)
* [阮一峰 ES6 入门](http://es6.ruanyifeng.com/)
* [compat table](http://kangax.github.io/compat-table/es6/)
* [Can I use](https://caniuse.com)

## Overview (New features in ES6)
* Constants
* Scoping
* Arrow Functions
* Extended Parameter Handling
* Template Literals
* Extended Literals
* Enhanced Regular Expression
* Enhanced Object Properties
* Destructuring Assignment
* Modules
* Classes
* Symbol Type
* Iterators
* Generators
* Map/Set * WeakMap/WeakSet
* Typed Arrays
* New Built-In Methods
* Promises
* Meta-Programming
* Internationalization * Localization

### Constants
Immutable variables, variable itself cannot be re-assigned (But object content can still be altered).
```
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
```
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
```
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
```
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
```
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

### Extended Parameter Handling, simple and intuitive default values for function parameters.
* Default Parameter Values
```
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
* Rest Parameter (扩展运算符)
```
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
```
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
```
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
```
    var o = { name: "Tom", age: 5 };

    // ES6
    fn`That is ${o.name} and he is ${o.age} years old.`;
    
    // ES5
    fn([ "That is ", " and he is ", " years old."], o.name, o.age);
```
* Raw String Access
```
    
```






