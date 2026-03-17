---
title: "Implement JavaScript's setInterval using setTimeout"
date: 2021-06-23T05:48:04+01:00
draft: false
author: "Orim Dominic Adah"
description: "Implement JavaScript's setInterval method using setTimeout"
tags: ["javascript", "setInterval", "setTimeout"]
hidemeta: false
disableShare: false
comments: false
showToc: true
TocOpen: false
cover:
  image: "../posts/images/implement-js-setinterval-using-settimeout.jpg"
  alt: "Implement JavaScript's setInterval using setTimeout"
  caption: ""
  relative: false
---

I was in a job interview pair programming session where I was asked to implement
JavaScript's `setInterval` method without using `setInterval` itself. I did
poorly at that interview for many reasons, including not knowing how to
implement this.

## The Question

Implement the setInterval function in such a way that executing `new SetInterval.prototype.start` will start the function passed to it and run the function at intervals `interval` milliseconds until the `SetInterval.prototype.clear` method is called.

For example:

```js
const runner = new SetInterval(() => console.log("hello"), 1000);

runner.start();

setTimeout(() => runner.clear(), 5000);
```

will log "hello" 5 times on the console/terminal at an interval of 1000ms.

I was asked to use the following snippet as a template for my implementation:

```js
function SetInterval(fn, interval) {}

SetInterval.prototype.start = function () {};

SetInterval.prototype.clear = function () {};
```

This question opened me up to a lot of my flaws in understanding JavaScript.

## The Solution

After reading Michael Zheng's implementation at [Implement setInterval with setTimeout](https://michaelzheng.medium.com/implement-setinterval-with-settimeout-bfe1f467731f), I modified his implementation to fit the interview question.

```js
function SetInterval(fn, interval) {
  this.fn = fn.bind(this);
  this.interval = interval;
  this.toContinue = true;
}

SetInterval.prototype.start = function () {
  const wrapper = () => {
    if (this.toContinue) {
      this.fn();
      // returning the id in case it may be needed.
      // Not necessary for this implementation
      return setTimeout(wrapper, this.interval);
    }
    return;
  };
  wrapper();
};

SetInterval.prototype.clear = function () {
  this.toContinue = false;
};

const theFn = new SetInterval(function () {
  console.log("hello");
}, 1000);

theFn.start();

setTimeout(() => {
  theFn.clear();
}, 5000);
```

## The Explanation

Interpret the `SetInterval` function as a JavaScript class for easier understanding. See it as

```js
class SetInterval {
  constructor(fn, interval) {
    this.fn = fn.bind(this);
    this.interval = interval;
    this.toContinue = true;
  }
}
```

When a new `SetInterval` object is created via `new SetInterval(fn, interval)`, the function `fn` that will run, and the interval are both passed in.

The value of `this` in the function argument `fn` passed in, is replaced with the object
instance that will be created by the `SetInterval` constructor function using
`bind`. This is done so that any reference to `this` in `fn` will refer to the
object instance created by `SetInterval`. The result of executing `bind` is
passed to `this.fn`, which is `fn` but with its `this` referring to the object
created by the `SetInterval` constructor.

The object's `toContinue` property is set to track if executing `this.fn` at intervals should stop or not.

### How does the `start` method work?
```js
start () {
  const wrapper = () => {
    if(this.toContinue){
      this.fn()
      // returning the id in case it may be needed.
      // Not necessary for this implementation
      return setTimeout(wrapper, this.interval);
    }
    return;
  };
  wrapper()
}
```

`start` is a method on object instances created by `SetInterval`. When `start`
is executed, a reference to `wrapper` is first stored in `start`'s local memory,
and then `wrapper` is executed.

When `wrapper` is executed, it checks if its instance's `toContinue` property has a value of `true`. If the condition evaluates to `true`, `this.fn` is executed, else, it isn't executed.

 `wrapper` is set to be executed on the next `this.interval` milliseconds using
`setTimeout(wrapper, this.interval)`. `setTimeout(wrapper, this.interval)` stores the
`setTimeout` function in JavaScript's memory, and schedules `wrapper` to run in the next `this.interval` milliseconds.

### How does the `clear` method work?

```js
clear () {
  this.toContinue = false
}
```

When `this.clear` is executed, it sets the object's `toContinue` property to a `false`. This causes the next execution of `wrapper` to find out that `this.toContinue` is now `false`, and
then doesn't execute `this.fn` or store another `wrapper` function to be executed in another `this.interval` milliseconds.

That is why the code snippet below:

```js
const theFn = new SetInterval(function () {
  console.log("hello");
}, 1000);

theFn.start();

setTimeout(() => {
  theFn.clear();
}, 5000);
```

will log "hello" five times.

[Clock Illustration](https://dryicons.com/free-icons/clock)
