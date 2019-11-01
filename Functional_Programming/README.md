# Functional Programming

⋅⋅* Functional programming is a paradigm of building computer programs using expressions and functions without mutating state and data.
⋅⋅* By respecting these restrictions, functional programming aims to write code that is clearer to understand and more bug resistant. This is achieved by avoiding using flow-control statements (for, while, break, continue, goto) which makes the code harder to follow. Also, functional programming requires us to write pure, deterministic functions which are less likely to be buggy.

Before diving into functional programming, one needs to understand the difference between pure and impure functions.

## Pure vs. Impure Functions

Pure functions take some input and give a fixed output. Also, they cause no side effects in the outside world.

```javascript
const add = (a, b) => a + b;
```

Here, `add` is a pure function. This is because, for a fixed value of `a` and `b`, the output will always be the same.

```javascript
const SECRET = 42;
const getId = (a) => SECRET * a;
```

`getId` is not a pure function. The reason being that is uses the global variable `SECRET` for computing the output. If `SECRET` were to change, the `getId` function will return a different value for the same input. Thus, it is not a pure function.

```javascript
let id_count = 0;
const getId = () => ++id_count;
```

This is also an impure function, and that too for a couple of reasons - (1) it uses a non-local variable for computing its output, and (2) it creates a side effect in thw outside world by modifying a variable in that world.

![alt text](https://bs-uploads.toptal.io/blackfish-uploads/uploaded_file/file/55111/image-1569846449711-b437d21679e123bdb3d092ebc64937c4.png "getId impure illustration")

This can be troublesome if we had to debug this code.

What's the current value of `id_count` ? Which other function are modifying `id_count` ? Are there other functions relying on `id_count` ?

Because of these reasons, we only use pure functions in **functional programming**.

Another benefit of pure functions is that they can be parallelized and memoized. Have a look at the previous two functions. It's impossible to parallelize or memoize them. This helps in creating performant code.


## The tenents of Functional Programming

So far, we have learned that functional programming is dependent on a few rules. They are as follows.

1. Don't mutate data
2. Use pure functions: fixed output for fixed inputs, and no side effects
3. Use expressions and declarations
   
When we satisfy these conditions, we can say our code is functional.

## Functional programming in JavaScript

JavaScript already has some functions that enable functional programming. Example: 

[String.prototype.slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/slice)
[Array.prototype.filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
[Array.prototype.join](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join)

On the other hand, [Array.prototype.forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach), [Array.prototype.push](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push) are impure functions.

One can argue that `Array.prototype.forEach` is not an impure function by design but think about it -- it's not possible to do anything with it except mutating non-local data or doing side effects. Thus, it's okay to put it in the category of impure functions.

Also, JavaScript has a [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const) declaration, which is perfect for functional programming since we won't be mutating any data.

## Pure Functions in JavaScript

Let's look at some of the pure functions (methods) given by JavaScript.

### Filter

As the name suggests, this filters the array.

```javascript
array.filter(condition);
```

The condition here is a function that gets each item of the array, and it should decide whether to keep the item or not and return the truthy boolean value for that.

```javascript
const filterEven = x => x%2 === 0;
[1, 2, 3].filter(filterEven);
// [2]
```

Notice that `filterEven` is a pure function. If it had been impure, then it would have made the entire filter call impure.

### Map

`map` maps each item of array to a function and creates a new array based on the return values of the function calls.

```javascript
array.map(mapper);
```

`mapper` is a function that takes an item of an array as input and returns the output.

```javascript
const double = x => 2 * x;
[1, 2, 3].map(double);
// [2, 4, 6]
```

### Reduce

`reduce` reduces the array to a single value.

```javascript
array.reduce(reducer);
```

`reducer` is a function that takes the accumulated value and the next item in the array and returns the new value. It is called like this for all values in the array, one after another.

```javascript
const sum = (accumulatedSum, arrayItem) => accumulatedSum + arrayItem;
[1, 2, 3].reduce(sum);
// 6
```

![alt text](https://bs-uploads.toptal.io/blackfish-uploads/uploaded_file/file/55112/image-1569846477173-d38787256500eeb7e2464c036280c6e0.png "reduce call illustration")

### Concat

`concat` adds new items to an existing array to create a new array. It's different from `push()` in the sense that `push()` mutates data, which makes it impure.

```javascript
[1, 2].concat([3, 4]);
// [1, 2, 3, 4]
```

You can do the same using the [spread](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) operator.

```javascript
[1, 2, ...[3, 4]]
```

### Object.assign

`Object.assign` copies values from the provided object to a new object. Since functional programming is predicated on immutable data, we use it to make new objects based on existing objects.

```javascript
const obj = {a: 2};
const newObj = Object.assign({}, obj);
newObj.a = 3;
obj.a;
// 2
```

With the advent of [ES6](http://es6-features.org/), this can also be done using the spread operator.

```javascript
const newObj = {...obj};
```

### Creating Your Own Pure Function

We can create our pure function as well. Let's do one for duplicating a string `n` number of times.

```javascript
const duplicate = (str, n) => n < 1 ? '' : str + duplicate(str, n-1);
```

This function duplicates a string `n` times and returns a new string.

```javascript
duplicate('hooray!', 3);
// hooray!hooray!hooray!
```

### Higher-order Functions

Higher-order functions are functions that accept a function as an argument and return a function. Often, they are used to add to the functionality of a function.

```javascript
const withLog = (fn) => {
  return (...args) => {
    console.log(`calling ${fn.name}`);
    return fn(...args);
  };
};
```

In the above example, we create a `withLog` higher-order function that takes a function and returns a function that logs a message before the wrapped function runs.

```javascript
const add = (a, b) => a + b;
const addWithLogging = withLog(add);
addWithLogging(3 , 4);
// calling add
// 7
```

`withLog` HOF can be used with other functions as well and it works without any conflicts or writing extra code. This is the beauty of a HOF.

```javascript
const hype = s => s + '!!!';
const hypeWithLogging = withLog(hype);
hypeWithLogging('Sale');
// calling hype
// Sale!!!
```

One can also call it without defining a combining function.

```javascript
withLog(hype)('Sale');
// calling hype
// Sale!!!
```

### Currying

Currying means breaking down a function that takes multiple arguments into one or multiple levels of higher-order functions.

Let's take the `add` function.

```javascript
const add = (a, b) => a + b;
```

When we are to curry it, we rewrite it distributing arguments into multiple levels as follows.

```javascript
const add = a => {
  return b => {
    return a + b;
  };
};
add(3)(4);
// 7
```

The benefit of currying is memoization. We can now memoize certain arguments in a function call so that they can be reused later without duplication and re-computation.

```javascript
// assume getOffsetNumber() call is expensive
const addOffset = add(getOffsetNumber());
addOffset(4);
// 4 + getOffsetNumber()
addOffset(6);
```

This is certainly better than using both arguments everywhere.

```javascript
// (X) DON'T DO THIS
add(4, getOffsetNumber());
add(6, getOffsetNumber());
add(10, getOffsetNumber());
```

We can also reformat our curried function to look succinct. This is because each level of the currying function call is a single line return statement. Therefore, we can use [arrow function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) in ES6 to refactor it as follows.

```javascript
const add = a => b => a + b;
```

### Composition

In mathematics, composition is defined as passing the output of one function into input of another so as to create a combined output. The same is possible in functional programming since we are using pure functions.

To show an example, let's create some functions.

The first function is range, which takes a starting number `a` and an ending number `b` and creates an array consisting of numbers from `a` to `b`.

```javascript
const range = (a, b) => a > b ? [] : [a, ...range(a+1, b)];
```

Then we have a function multiply that takes an array and multiplies all the numbers in it.

```javascript
const multiply = arr => arr.reduce((p, a) => p * a);
```

We will use these functions together to calculate factorial.

```javascript
const factorial = n => multiply(range(1, n));
factorial(5);
// 120
factorial(6);
// 720
```

The above function for calculating factorial is similar to `f(x) = g(h(x))`, thus demonstrating the composition property.


## Conclusion

** Functional Programming ** is a [well-researched](https://dtai.cs.kuleuven.be/topics/fp) and [robust](https://www.researchgate.net/publication/323714122_An_Overview_of_Practical_Impacts_of_Functional_Programming) paradigm of writing computer programs. With [the introduction of ES6](http://es6-features.org/),JavaScript allows for a much better functional programming experience than ever before.



## UNDERSTANDING THE BASICS

1. What is functional programming?
   Functional programming is a paradigm of building computer programs using declarations and expressions.
2. Is JavaScript a functional programming language or object-oriented?
   Thanks to new developments in ES6, we can say that JavaScript is both a functional as well as object-oriented programming language because of the various first-class features it provides.
3. What is the advantage of functional programming?
   Functional programming ensures simpler flow control in our code and avoids any surprises in the form of variable and state changes. All this helps us avoid bugs and understand out code easily.
4. What are other functional programming languages?
   Lisp, Erlang, Haskell, Closure, and Python are other functional programming languages. In these, Haskell is a purely functional programming language in the sense that it allows no other paradigm of programming.
5. What is ES6?
   ES6 or ECMAScript 6 is a new version of JavaScript that includes many new features like arrow functions, constants, and spread operators, among many others.
