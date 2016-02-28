title: JavaScript Scopes and Closures
output: index.html
style: style.css
author:
  name: Guilherme Rodrigues
  twitter: first_doit
  url: http://firstdoit.com

--

# JavaScript Scopes and Closures
## A super fast introduction

--

### Contents

- Functions
- Scopes
- Closures
- `this` keyword

--

# First things first!

--

### What is a function?

> Generally speaking, a function is a "subprogram" that can be called by code external to the function.

--

### What is scope?

> In JavaScript, scope is the set of variables and functions you have access to.

--

```js
// Global Scope
var foo = 1;
var myFunction = function (qux) {
  // myFunction Scope
  var bar = 2;
  // Function arguments work
  // just like local variables.
  // They are also in the scope.
  console.log(qux);
};
console.log(foo) // 1
console.log(bar) // ReferenceError!
console.log(qux) // ReferenceError!
```

--

> In JavaScript, scopes are created by **functions**. They aren’t created by `for` or `while` loops or expression statements like `if` or `switch`.

--

### New function = new scope.

This creates the **scope chain**: inner functions have access to the scope of outer functions.

```js
// Global Scope
var myFunction = function () {
  // myFunction Scope
  var myOtherFunction = function () {
    // myOtherFunction Scope
  };
};
```

--

### What is a closure?

> Whenever you see a function within another function, the *inner* function **has access to** the scope of the *outer* function.

--

```js
var outerFunction = function () {
  // outerFunction Scope
  var foo = 1;
  console.log(foo); // 1
  var innerFunction = function () {
    // innerFunction Scope
    // access both scopes
    var bar = 2;
    console.log(foo); // 1
    console.log(bar); // 2
  };
  return innerFunction;
};
```

--

> A closure is the combination of a function **bundled together** with *references* to its scope chain. It is created at **function creation time**.

--

When an inner function is created, the JavaScript VM saves *references* to all the variables in all the parent scopes.

```js
var makeFunc = function () {
  // makeFunc Scope
  var name = "Mozilla";
  var displayName = function () {
    // displayName has access
    // to makeFunc Scope
    console.log(name);
  }
  return displayName;
}
console.log(name) // ReferenceError!
var myFunc = makeFunc();
myFunc(); // Mozilla
```

--

### What we got so far

- Functions create new scopes
- Functions have access to all outer scopes
- A closure is *a function* bundled with *references* to the surrounding scope.

--

Enough theory!

--

### Closure applications

- Data privacy
- Stateful functions

--

# Data privacy

--

Imagine you want to create a person  
object with a `looksLike` function,  
but hide their age:

```js
function createPerson () {
  return {
    age: 25,
    looksLike: function () {
      return this.age - 5;
    }
  };
}
var dude = createPerson();
dude.looksLike() // 20
dude.age // 25 - oops!
```

--

Closures are useful to hide data  
from the outside world.

```js
function createPerson () {
  var age = 25;
  return {
    looksLike: function () {
      return age - 5;
    }
  };
}
var dude = createPerson();
dude.looksLike() // 20
dude.age // undefined
```

--

Questions?

--

# Stateful functions

--

Functions can return functions.  
They are called **stateful functions**, because they *remember* the environment they were created in.

```js
var createMyFunction = function (myArgument) {
  return function () {
    console.log(myArgument);
  }
};
```

--
# Detour!

**Pass by reference** or **Pass by value**?

--

> In JavaScript, Primitives are passed by value, Objects are passed by "copy of a reference".

--

```js
function replace(ref) {
  // this code does _not_ affect the object passed
  ref = {};
}
function update(ref) {
  // this code _does_ affect the _contents_ of the object
  ref.key = 'newvalue';
}
var a = { key: 'value' };
// a still has its original value - it's unmodfied
replace(a);
// the _contents_ of 'a' are changed
update(a);
```

--

Going back to our example:  
When `createMyFunction` is **executed**...  
The *inner function* is created.

```js
var createMyFunction = function (myArgument) {
  return function () {
    // Closure created! myArgument == 1
    console.log(myArgument);
  }
};
var myFunction = createMyFunction(1);
myFunction() // 1 - primitive
```

--

###  Partial Application

Partial application is a concept in functional programming.
Until all arguments are given, a new function is returned.

```js
function add(x, y) {
  return x + y;
}
// NOT JAVASCRIPT!
var add5 = add(5); // Only applied first arg
add5(2); // 7
```

--

We can use stateful functions to create something similar to *partial application*.

```js
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}
var add5 = makeAdder(5);
var add10 = makeAdder(10);
add5(2);  // 7
add10(2); // 12
```

--

(We can make the original example work with `Function.bind`. Can you find out how? Homework 😜)

```js
function add(x, y) {
  return x + y;
}
var add5 = add.bind(???)
```

--

Questions?

--

### The dreaded *async for* bug

Let's say you have an array of buttons in the DOM.
You want to create a click handler for each of them. <div class='buttons ex1'><button>Foo</button><button>Bar</button><button>Qux</button></div>
```js
var buttons = document.querySelectorAll('button');
for (var i = 0; i < buttons.length; i++) {
  var button = buttons[i];
  button.addEventListener('click', function() {
    alert(button.innerText)
  })
}
```
<script>
var buttons = document.querySelectorAll('.ex1 button');
for (var i = 0; i < buttons.length; i++) {
  var button = buttons[i];
  button.addEventListener('click', function() {
    alert(button.innerText)
  })
}
</script>

--

All buttons alert `Qux`!

--

What's happening? 🤔

> In JavaScript, closures are created every time a function is created, at function creation time.

--

All functions share the reference for the same variable!  
Each loop, the variable is overwritten.  
By the end, all point to the last button. 🐼

```js
var buttons = [...];
for (var i = 0; i < buttons.length; i++) {
  // Overwrites same reference!
  var button = buttons[i];
  button.addEventListener('click', function() {
    // Reference for button saved!
    alert(button.innerText)
  })
}
```

--

# Solution:
# Stateful function!

--
<div class='buttons ex2'><button>Foo</button><button>Bar</button><button>Qux</button></div>
```js
var buttons = document.querySelectorAll('button');
for (var i = 0; i < buttons.length; i++) {
  var button = buttons[i];
  var createClickHandler = function (el) {
    return function() {
      alert(el.innerText)
    }
  }
  button.addEventListener('click', createClickHandler(button))
}
```
<script>
var buttons = document.querySelectorAll('.ex2 button');
for (var i = 0; i < buttons.length; i++) {
  var button = buttons[i];
  var createClickHandler = function (el) {
    return function() {
      alert(el.innerText)
    }
  }
  button.addEventListener('click', createClickHandler(button))
}
</script>

--

What we have now (IIFE version):

```js
var buttons = [...];
for (var i = 0; i < buttons.length; i++) {
  var button = buttons[i];
  button.addEventListener('click', (function (el) {
    return function() {
      alert(el.innerText)
    }
  })(button))
}
```

--

Et voilà! 😁
Each button alerts its text.

--

Questions?

--

### Bonus points: using forEach

When dealing with arrays, you can avoid `for` by using `Array.forEach`. <div class='buttons ex3'><button>Foo</button><button>Bar</button><button>Qux</button></div>
```js
var buttons = Array.prototype.slice.call(
  document.querySelectorAll('button')
);
buttons.forEach(function(button) {
  button.addEventListener('click', function() {
    alert(button.innerText)
  })
})
```
<script>
var buttons = Array.prototype.slice.call(document.querySelectorAll('.ex3 button'));
buttons.forEach(function(button) {
  button.addEventListener('click', function() {
    alert(button.innerText)
  })
})
</script>

--

# Context isnt Scope

--

### Context is the `this` keyword

`this` is determined by *"where"* the function was called.

```js
var sayAge = function () {
  console.log(this.age);
};
var a = { age: 1, sayAge: sayAge };
var b = { age: 2, sayAge: sayAge };
a.sayAge() // 1
b.sayAge() // 2
```

--

### `this` is a little complicated...

`this` is `window` if executed on the global context.

```js
var wat = function () {
  console.log(this);
};
wat(); // Window
```

--

### `this` confusion.

```js
var a = {
  age: 1,
  sayAge: function () {
    console.log(this.age);
  }
};
var b = { age: 2 };
a.sayAge(); // 1
var withoutContext = a.sayAge;
withoutContext(); // undefined
b.sayAge = a.sayAge;
// b.sayAge = withoutContext;
b.sayAge(); // 2
```

--

### Advanced `this`

`Function.apply()`  
`Function.call()`  
`Function.bind()`

--

### Recap: closure applications

- Data privacy
- Functional programming (sort of)
- Stateful functions

--

Questions?

--

# Thanks!

## 💥💥💥

--

## Resources

- http://firstdoit.com/closures/
- https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures
- https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Statements/let
- https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/
- https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind
- http://stackoverflow.com/questions/13104494/does-javascript-pass-by-reference
