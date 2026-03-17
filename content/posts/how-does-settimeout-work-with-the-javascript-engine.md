---
title: "How does setTimeout Work with the JavaScript Engine?"
date: 2023-01-04T11:10:46+01:00
author: "Orim Dominic Adah"
description: "A guide on how setTimeout works with JavaScript the JavaScript engine"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["javascript", "setTimeout"]
cover:
  image: "../posts/images/settimeout-javascript-engine.png"
  alt: "setTimeout JavaScript"
  caption: ""
  relative: false
---

If one wants a function to be executed just after a period of time _T_ in
JavaScript, what do they do? They dump it in a `setTimeout` and surely, just
immediately after _T_ has elapsed, the function will be run. Right?

Can you predict what happens when the code below is run? You should try it out
in a Node.js REPL or the console.

```js
const extractTime = (date) => date.toTimeString().split(" ")[0];

var start = new Date();

console.log("starting at", extractTime(new Date())); // #1

setTimeout(() => {
  const time = extractTime(new Date());
  console.log(`From setTimeout. Logged at ${time}`); // #2
}, 1000);

while (new Date() - start < 3000) {
  // empty while loop
} // #3
```

## What I ordered vs What I got

For anyone that has not worked with code like this before, the expectation would
be that #2 will be logged about 1 second after #1, but it was not. It was logged
about 3 seconds after #1. Why?

![setTimeout on the console](../images/settimeout-on-the-console.png)

## Single-threaded, Synchronous JavaScript

JavaScript is a single-threaded, synchronous language. What that means is that
it can run only one operation at a time and it will handle each operation
synchronously - one after another. Now that's interesting; because how does it
handle the operations that are denoted with the keyword _async_?

JavaScript runs on engines. It could be V8, Chakra or any other engine, but
JavaScript does not run on its own. The purpose of the engine is to convert
JavaScript code (high-level language) to code that computers can understand.
These engines run in the background background thread while JavaScript runs in
the main thread.

Operations such as Promises and setTimeout are asynchronous. When they are
triggered to run, they are not run on the JavaScript main thread; they are sent
to the background thread instead. When the background thread is done running the
async operations, it sends the result to a _queue_.

As long as there is no operation running on JavaScript's main thread (the _call
stack_) when the the result was sent to the queue, JavaScript will accept the
result from the queue and do what is required with the result.

How does this influence the result from the code highlighted in the beginning of
this article?

## How setTimeout works with the JavaScript Engine

When the engine starts to run the code highlighted, the current time is logged
to the console at #1. It encounters the `setTimeout` function and at this point,
it passes the function placed in `setTimeout` and the timeout to the background
thread. The background thread starts a timer and holds on to this function for
the time specified in the timeout. When the timeout is elapsed, the background
thread releases the function to the queue.

At this point, the `while` loop is still running (is on the call stack) and it
blocks the main thread from accepting items from the queue. It will block the
main thread for 3 seconds.

When the `while` loop is popped off the call stack 3 seconds later, the main
thread is open to deque items from the queue. At this point, it deques the
function from the queue and executes it. That is why line #2 is executed 3
seconds after instead of 1 second after as directed in the setTimeout
declaration.
