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
- Scope
- Closure
- Examples
- ES2015

--

# What is a function?

--

> Generally speaking, a function is a "subprogram" that can be called by code external to the function.

--

# What is scope?

--

> In JavaScript, scope refers to the current context of your code.

--

## Scope is where state lives.

```js
// Scope A
var foo = 1;
var myFunction = function () {
  // Scope B
  var bar = 2;
};
console.log(foo) // 1
console.log(bar) // ReferenceError!
```

--

> All scopes in JavaScript are created with Function Scope only. They aren’t created by for or while loops or expression statements like if or switch.

--

## New function = new scope.

```js
// Scope A
var myFunction = function () {
  // Scope B
  var myOtherFunction = function () {
    // Scope C
  };
};
```

--

# What is a closure?

--

> Whenever you see a function within another function, the inner function has access to the scope of the outer function. This is called Lexical Scope or Closure.

--

```js
var outerFunction = function () {
  // Scope A
  var foo = 1;
  console.log(foo); // 1
  var innerFunction = function () {
    // Scope B - access both scopes
    var bar = 2;
    console.log(foo); // 1
    console.log(bar); // 2
  };
};
```

--

> In a nutshell, a closure is the combination of a function bundled together (enclosed) with references to it’s surrounding state (the lexical environment).

--

> In JavaScript, closures are created every time a function is created, at function creation time.

--

Yeah... so?

--

Time for **examples**!

--

### The dreaded *async for* bug

What should happen here?

```js
for (var i = 0; i < 10; i++) {
    setTimeout(function () {
      console.log(i);
    }, 100);
}
```

--

If you guessed:

`1, 2, 3, ...`

--

I'm sorry to disappoint you:

`10, 10, 10, 10, 10, ...`

--

What's happening?

```js
for (var i = 0; i < 10; i++) {
    setTimeout(function () {
      console.log(i);
    }, 100);
}
```
--

> In JavaScript, closures are created every time a function is created, at function creation time.

--

```js
for (var i = 0; i < 10; i++) {
    something = function () {
      // Scope created!
      console.log(i);
    };
    setTimeout(something, 100);
}
```
--

```js
for (var i = 0; i < 10; i++) {
    something = function () {
      // Reference for i saved
      console.log(i);
    };
    setTimeout(something, 100);
}
```

--

100 ms later:

```js
// i is in scope, with value 10
var i;
(function () {
  console.log(i); // 10!
})();
```

--

# Solution: Closure!

--

Functions can return functions.  
Presenting: the **stateful function**.

```js
gimmeSomething = function (myArgument) {
  return function () {
    console.log(myArgument);
  }
};
```

--

Ma, look at my scopes!

```js
gimmeSomething = function (myArgument) {
  // Scope A - myArgument is a local var
  return function () {
    // Scope B - can access A
    console.log(myArgument);
  }
};
```

--

When `gimmeSomething` is **executed**...
The *inner function* is created.

```js
gimmeSomething = function (myArgument) {
  return function () {
    // Closure created! myArgument == 1
    console.log(myArgument);
  }
};
gimmeSomething(1);
```

--

It's a trap!

```js
gimmeSomething = function (myArgument) {
  return function () {
    console.log(myArgument);
  }
};
for (var i = 0; i < 10; i++) {
  // Aha! i is trapped!
  setTimeout(gimmeSomething(i), 100);
}
```

--

Et voilà:

`1, 2, 3, 4, 5, ...`

--

### Bonus points: using forEach

When dealing with arrays, you can avoid `for` by using `Array.forEach`.

```js
var myArray = [1, 2, 3];
myArray.forEach(function (i) {
  // Works right off the bat!
  setTimeout(function () {
    console.log(i);
  }, 100);
});
```

--

### Bonus points: ES2015 let

In `ES2015`, you can declare variables with `let`.

`let` is special because it's **block scoped**.

--

```js
function varTest() {
  var x = 31;
  if (true) {
    var x = 71;  // same variable!
    console.log(x);  // 71
  }
  console.log(x);  // 71
}
```

--

```js
function letTest() {
  let x = 31;
  if (true) {
    let x = 71;  // another variable
    console.log(x);  // 71
  }
  console.log(x);  // 31
}
```

--

> You can use the let keyword to bind variables locally in the scope of for loops. This is different from the var keyword in the head of a for loop, which makes the variables visible in the whole function containing the loop.

--

Also works!

```js
for (let i = 0; i < 10; i++) {
  setTimeout(function () {
    console.log(i);
  }, 100);
}
```

`1, 2, 3, 4, 5, ...`

--

### The private data

Closures are also useful to hide data from the outside world.

--

# Thanks!

--

## Resources

- http://firstdoit.com/closures/
- https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions
- https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Statements/let
- https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/