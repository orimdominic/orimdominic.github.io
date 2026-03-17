---
title: "What Is an Asynchronous Function in JavaScript?"
date: 2022-03-25T20:02:58+01:00
author: "Orim Dominic Adah"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["javascript", "asynchronous"]
cover:
  image: "../posts/images/asynchronous-function-javascript.png"
  alt: "Asynchronous function javascript"
  caption: ""
  relative: false
---

When you look at any of the hands of a ticking clock, you will find out that it moves sequentially. It points at 1 before it points at 2, and then it points at 3 and it continues. It does not point at 4 and then 1 and then 9, in a random manner. It follows a sequence.

Imagine that the hand is the JavaScript runtime and each number is a function. The clock's hand, which represents the JavaScript runtime, determines what function is executed by pointing at the number. When it points at 1, `function1` runs, and when it points at 2, `function2` runs.

When it points at 3 and `function3` says

> _You've asked me to run and I will, but I will run in the background. Go
> ahead and ask the functions after me to run while I run in the background. When I run to completion, I will notify you and send you the return value of my execution_

then `function3` is an asynchronous function.

When `function3` is done running, it notifies the JavaScript runtime. When the runtime is done handling all synchronous operations, it handles the notification from `function3` and does whatever should be done done with the value returned from `function3`.

[Callback functions](../what-is-a-javascript-callback-function) are usually attached to asynchronous functions so that when the asynchronous function returns with a value, the callback function would automatically get executed with the return value.

## Examples of Asynchronous Functions

It would make more sense help to add practical use cases of asynchronous functions.

### setTimeout

`setTimeout` is an example of an asynchronous function. When `setTimeout` is executed with a function `f` and a timeout `n` passed in, it captures `f` and holds it in the backgroud for `n` milliseconds. After `n`  milliseconds, it notifies the JavaScript runtime that it has run to completion.

The JavaScript runtime receives this notification and when it is done executing all synchronous operations (like the `console.log` operations in the code snippet below), it runs the callback function attached to the `setTimeout` call, causing it to log "I am function f" to the console.

That is why "I am function f" appears after "3" on the console.

```js
console.log(1);
console.log(2);
setTimeout(() => {
  console.log("I am function f");
}, 0);
console.log(3);
```

### fetch

According to [MDN docs](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API), the `fetch` API provides an interface for fetching resources. It is used for making network request most of the time.

A network request is an I/O operation. I/O operations do not have a predictable completion time. They could be slowed down by
- a poor network
- low computer speed
- the resource server taking long to respond

and because of this, the JavaScript engine executes `fetch` in the background. If `fetch` is executed on the main thread, delays in running it to completion will block other operations from executing until the `fetch` operation been completely executed, and that will cause poor user experience.

{{< figure caption="Rejected or Fulfilled" align=center src="../images/rejected_fulfilled_promises.png" width="250px" >}}

When `fetch` is executed by the JavaScript runtime, it immediately returns a _promise_. This is to show that the result of the execution is pending and a result is to be expected.

A promise is like an empty gift box given to you when someone makes a promise to you. The box has two empty partitions when the "promiser" has not delivered on their promise. One partition gets filled with the real gift item (**fulfilled**) and the other partition gets filled with a reason why the gift could not be delivered (**rejected**). Only one of these boxes can be filled. When either of the boxes is filled, the promise is said to have been **settled**.

Both partitions have a callback function attached to them that get executed when the promise is settled. The `then` method accepts the callback function as an argument for the fulfilled partition. The `catch` method accepts the callback function as an argument for the rejected partition.

```js
const prom = fetch()

prom
.then(runOnFulfilled)
.catch(runOnRejected)

```

In summary, when a fetch request is executed, it immediately returns a promise object and the JavaScript runtime runs the request in the background. When completed, the promise is settled and the JavaScript runtime is notified. When the JavaScript runtime is done executing all synchronous operations (for an event loop), it executes one of the callback functions attached to the promise. The callback function to run depends on whether the promise was rejected or fulfilled.

## Conclusion

An asynchronous function is one that completely runs at a later time and will not prevent the statements after it from running because it is not run on the main thread. It will notify the runtime on completion for the runtime to take action after executing all synchronous operations.

[People illustrations by Storyset](https://storyset.com/people)
