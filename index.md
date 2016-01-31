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

## New functions = new scope.

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

# The dreaded "async for" loop bug

--

# Solution: Closure!

--

## Resources

- http://firstdoit.com/closures/
- https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions
- https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/
