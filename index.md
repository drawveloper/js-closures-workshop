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

- Primitives and objects
- Variables and references
- Pass by value
- Functions
- Scopes
- Closures
- Context

--

What we'll solve today:

--

How to hide `age` but still  
make `looksLike` work?

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

The mistery of the broken buttons. <div class='buttons ex0'><button>Foo</button><button>Bar</button><button>Qux</button></div>
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
var buttons = document.querySelectorAll('.ex0 button');
for (var i = 0; i < buttons.length; i++) {
  var button = buttons[i];
  button.addEventListener('click', function() {
    alert(button.innerText)
  })
}
</script>

--

# First things first!

--

##Let's revisit some  
##**JavaScript Insides.**

--

JavaScript has **primitives** and **objects**.

--

### Primitives

- string `'foo'`
- number `123`
- boolean `true`
- undefined
- null

--

### Objects

- Objects are aggregations of properties.
- Properties can reference a primitive.
  - `{ foo: 'bar', qux: 123 }`
- Properties can reference an object.
  - `{ foo: { bar : '123' } }`

--

### Primitive Values

When you initialize a variable with a primitive,  
this variable holds **the actual value**.  

```js
var myString = 'test';
var myNumber = 123;
```

These variables hold **primitive values**.

--

### Reference Values

When you initialize a variable with an object,  
this variable holds **the reference to the object**.

```js
var myObject1 = { foo: 'bar' };
var myObject2 = { qux: 123 };
```

These variables hold **reference values**.

--

This is **super incredibly important!**  
A variable *never holds the object itself*.  
The object is far away, in a magical land *(heap)*.  
You only get a **reference** to the object.

```js
var myRefToObject = { foo: 'bar' };
```

--

### Recap

- Primitives
- Objects
- Primitive values
- Reference values

--

Let's talk about **copying values.**

--

### Copying primitive values

Assigning variables copies values.

```js
var a = 1;
// Creates a copy of the primitive.
var b = a;
console.log(b); // 1
a = 2;
console.log(b); // 1
```

--

### Copying reference values

Assigning variables copies reference values.  
(The same as primitive values!)

```js
var a = { foo: 'bar' };
// Creates a copy of the reference.
var b = a;
console.log(b); // { foo: 'bar' }
a = 2;
console.log(b); // { foo: 'bar' }
```

--

### Copying reference values

However, references still point  
to the same object and can modify it.

```js
var a = { foo: 'bar' };
// Create a copy of the reference.
var b = a;
b.foo = 2;
console.log(a); // { foo: 2 }
```

--

## JavaScript is **pass by value.**

--

### Pass by value

When you call a function, the  *arguments*  
(or *parameters*) are **copied** into the function.

```js
var foo = function (myParameter) {
  myParameter = 'bar'; // Modify your copy
}
var a = 1;
foo(a);
console.log(a); // 1
```

--

### Pass by value

Doesn't matter if they are primitives or references.

```js
var foo = function (myParameter) {
  // myParameter is the COPY of the reference
  myParameter = 'bar'; // Modify your copy
}
var a = { foo: 123 };
foo(a);
console.log(a); // { foo: 123 }
```

--

### Recap

- A value is a value is a value
- Values are always copied around
- JavaScript is pass by value

--

### What is a function?

> A function is a "subprogram" that can be called.

--

### What is scope?

> In JavaScript, scope is the set of  
variables and functions you have access to.  
Scopes are created by **functions**.  

--

### Local variables

When you declare a variable inside a function,  
it's a **local variable** to that function.  
That means it is **in that scope**.

```js
// Global Scope
var foo = 1;
var myFunction = function () {
  // myFunction Scope
  // bar is a local variable
  var bar = 2;
};
console.log(foo); // 1
console.log(bar); // ReferenceError!
```

--

Scopes are the geography of your code.  
The global scope is a plain.  

![Plains](img/plains.jpg)

--

A simple function is a hill.  
That function can look down  
and see everything in the plain.  

![Hill](img/hill.jpg)

--

Nested functions are mountains.  
The more deeply nested, the higher.

![Mountain](img/mountains.jpg)

--

### New function = new scope.

This creates the **scope chain**: inner functions  
have access to the scope of outer functions.  
(Because you can look down and see.)

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

A closure is the "**memory**" of a function.  
A function can *remember* where it was created.  
No matter where it ends up, it can always talk about  
the things in the hills down below (outer scopes).

--

### What is a closure?

> Whenever you see a function **within** another function,  
the *inner* function **has access to** the scope of the *outer* function.

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

When an inner function is created, the JavaScript VM saves  
*references* to all the variables in all the parent scopes.

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

### Recap

- Functions create new scopes
- Functions have access to all outer scopes
- A closure is *a function* bundled with  
*references* to the surrounding scopes

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

All functions **share the same** variable!  
Each loop, the **same** variable is overwritten with a new reference.  
They will always point to only one button.  
It happens to be the last because it's the last value written.

```js
var buttons = [...];
for (...) {
  // Overwrites same reference!
  var button = buttons[i];
  button.addEventListener('click', function() {
    // Shares reference for unique "button" var.
    alert(button.innerText)
  })
}
```

--

# Solution:
# Stateful function!

--

What if we create a closure to  
**force creating a copy** of the reference?

```js
var createClickHandler = function (el) {
  return function() {
    alert(el.innerText);
  }
};
```

--
Et voilà! 😁
<div class='buttons ex2'><button>Foo</button><button>Bar</button><button>Qux</button></div>
```js
var buttons = document.querySelectorAll('button');
for (var i = 0; i < buttons.length; i++) {
  var button = buttons[i];
  var createClickHandler = function (el) {
    // will be stored in var clickHandler
    return function() {
      alert(el.innerText);
    }
  };
  var clickHandler = createClickHandler(button);
  button.addEventListener('click', clickHandler);
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

### `this` defaults to `window`

`this` is `window` if executed on the global context.

```js
var wat = function () {
  console.log(this);
};
wat(); // Window
```

--

### Common `this` confusion.

```js
var a = {
  age: 1,
  sayAge: function () {
    console.log(this.age);
  }
};
var b = { age: 2 };
a.sayAge(); // 1
//var withoutContext = a.sayAge;
//withoutContext(); // undefined
//b.sayAge = withoutContext;
//b.sayAge(); // 2
```

--

### Common `this` confusion.

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
//b.sayAge = withoutContext;
//b.sayAge(); // 2
```

--

### Common `this` confusion.

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
b.sayAge = withoutContext;
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
- Stateful functions

--

Questions?

--

# Thanks!

## 💥💥💥

--

- https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures
- https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/
- http://stackoverflow.com/questions/13104494/does-javascript-pass-by-reference
- http://stackoverflow.com/questions/373419/whats-the-difference-between-passing-by-reference-vs-passing-by-value
- http://stackoverflow.com/questions/13266616/primitive-value-vs-reference-value
