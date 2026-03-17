---
title: "What is a Callback Function in JavaScript"
date: 2022-08-07T18:08:14+01:00
author: "Orim Dominic Adah"
description:
  "A function that is passed to another function to be executed in that
  function."
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["javascript", "callback function"]
cover:
  image: "../posts/images/callback-function-javascript.png"
  alt: "Callback Function JavaScript"
  caption: ""
  relative: false
---

You know how you ask someone to _call you back_ in a phone conversation? That's
what callback functions are. They are functions to be called later; after
something has happened.

In order to grasp this in practice, one needs to understand two things

1. that there is a difference between a function name `fn` and a function call
   `fn()`,
2. that functions can be passed into functions as arguments, the same way that
   numbers, strings and arrays are passed into functions as arguments

## The difference between a function name and a function call

Let's consider the code snippet below

```js
function fn() {
  console.log("I am a sweet simple function");
}
```

We have defined a function `fn`. That means that we have created a function and
stored it under the identifier `fn`. If we log `fn` to the console with
`console.log(fn)`, we will see see the code that we created. It makes sense to say
that we have gotten fuel and we have given it to `fn`.

Fuel has the ability to burn, but it will not burn unless it is ignited. The
function that we have given to `fn` is like fuel. It can be executed but it
would not be executed unless we ignite it. How do we ignite it? By simply adding
parentheses to the name of the function in our code.

```js
fn(); // "I am a sweet simple function"
```

`fn` is the name of the function. `fn()` is its execution.

## Functions as Arguments

Let us consider the code snippet below

```js
function acceptArgumentAndLogIt(arg) {
  console.log(arg);
}

acceptArgumentAndLogIt("an argument"); // "an argument"
```

`acceptArgumentAndLogIt` simply accepts an argument under the identifier `arg`
and logs it. In the last line of the snippet, "an argument" is passed to
`acceptArgumentAndLogIt` and `arg` then represents "an argument". What happens
if instead of accepting an argument and logging it, we accept a function as an
argument, and run it. Let's see!

```js
function acceptFunctionAndRunIt(fn) {
  fn();
}

function logSomething() {
  console.log("something");
}

acceptFunctionAndRunIt(logSomething); // "something"
```

We defined two functions. `acceptFunctionAndRunIt` accepts a function and runs
it (as in our fuel analogy, it ignites it by appending parentheses to it). The
other `logSomething` holds the ability to log "something" to the console.

In the last line of the snippet, we call `acceptFunctionAndRunIt` and pass in
`logSomething` to it. `logSomething` will to be represented as `fn` within it
and because of this, `fn` will be a reference to `logSomething`. When
`acceptFunctionAndRunIt` runs, it sees `fn` and asks _what does `fn` refer to?_
The JavaScript runtime rightly tells it that it refers to the the function
assigned to the label `logSomething`. That function is retrieved and executed,
and then "something" appears on the console.

## What then is a Callback Function?

Any function A, that is passed into another function B to be executed within
function B is a callback function. The reason why it is called by that name is
because function A is usually passed into function B so that function B can call
it after running some operations. It will be easier to grasp if instead of
saying "callback", one says "call later", as in the phone conversation analogy.

Imagine that you have a phone conversation with a friend, and you need
information from them, but they don't have it at hand, so you say '_call me
back_ when you have it'. Your friend goes on to get the information and then
calls you or _ignites you_ afterwards. Let's make a code snippet out of this.

```js
function useInfo(info) {
  console.log(info);
}

function getInfo(giveInfoToFriend) {
  // getInfoFromBagOfSecrets() returns ["secret one","secret two"],
  // takes about 2 mins, hypothetically
  const info = getInfoFromBagOfSecrets();
  giveInfoToFriend(info);
}

getInfo(useInfo);
```

When the JavaScript runtime runs, it stores the two functions defined under
their respective identifiers. In the last line of the snippet, we call `getInfo`
and pass in `useInfo` as an argument. When `getInfo` runs, it accepts the
reference to the function assigned to `useInfo` as `giveInfoToFriend`, thus
within `getInfo`, `useInfo` is the same as `giveInfoToFriend`. Information is
gotten from the bag of secrets and is passed to `giveInfoToFriend`.

In order to execute `useInfo`, we add the parentheses and pass `info` to it. The
execution causes what `info` represents to be logged to the console. The
function passed in as `giveInfoToFriend` is a callback function because it was
passed in to be called at a later time, that is, when the information has been
retrieved.

## Real Life Examples

Callback functions exist in many places and you've seen them, you probably
didn't see them that way.

### `window.addEventListener("eventname", callbackFn)`

Remember, a callback function is one that is passed into another function to be
called at a later time. If we have
`window.addEventListener("click", callbackFn)` in our code, we do not know
**when** the user will click on the window, so we say _whenever the user clicks,
pass in the necessary arguments to `callbackFn` and run it_. `callbackFn` will
be executed at a later time, specifically when the user clicks on the window.

### `fs.read(fd, [options,] callbackFn)`

In Node.js, we have the ability to read files present on the computer. The
`fs.read` function is an asynchronous one, in otherwords, it may or may not be
completely run before the code that is written after it. Since we do not know
when it will be completely run, we pass in `callbackFn` when we execute it and
say _look, we don't know when you will be done running, but whenever you're done,
whatever result that you get, pass it into `callbackFn` and then run
`callbackFn`_.

**Image credits**

- [Worker illustrations by Storyset](https://storyset.com/worker)
