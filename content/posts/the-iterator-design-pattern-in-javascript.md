---
title: "The Iterator Design Pattern in JavaScript"
date: 2023-02-03T21:36:33+01:00
author: "Orim Dominic Adah"
description: "Describing and implementing the native iterator design pattern in JavaScript"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags:
  [
    "javascript iterator",
    "javascript iterable",
    "javascript iterator design pattern",
    "design pattern",
  ]
cover:
  image: "../posts/images/the-iterator-design-pattern-javascript.png"
  alt: "Iterator Design Pattern"
  caption: "Iterator Design Pattern"
  relative: false
---

## Design Patterns

In the field of programming, there exists a concept called 'design patterns'.
Design patterns are established solutions to recurring challenges in the field
of software design.

A sample challenge that requires the use of a pattern is that of an API
endpoint that returns a list of items. It may be fine for the endpoint to always
return all the items that the database has for every API call to the endpoint,
but it gets to a point where the list of items to return would be unnecessarily
large or some of the items in the list returned may not be needed by the client
making a request to the API. What happens now? The endpoint would need to
implement the pagination pattern.

Implementing the pagination pattern for the endpoint demands that the endpoint
requests for inputs so that consumers of the endpoint can fill in the number of
items they want returned from the database. It can also request inputs for
consumers to fill in the "start" and "end" position in the database from which
items should be returned. This solves the challenge of returning large volumes
of data that may not be needed by the client.

```HTTP
# API endpoint before implementing the pagination pattern
GET http://api.com/v1/items
```

```json
{
  "items": [
    {
      "name": "Item 1",
      "price": 20
    },
      {
      "name": "Item 2",
      "price": 10
    },
    ...998 more item objects
  ]
}
```

```HTTP
# API endpoint after implementing the pagination pattern
GET http://api.com/v1/items?limit=2&skip=3
```

```json
{
  "items": [
    {
      "name": "Item 4",
      "price": 13
    },
    {
      "name": "Item 5",
      "price": 24
    }
  ],
  "totalCount": 1000,
  "nextPage": 3,
  "previousPage": 1,
  "currentPage": 2
}
```

The endpoint that implements the pagination pattern returns only two item
objects as requested by the client via the _limit_ query parameter. This
implementation causes the response to have less item objects than the endpoint
that does not implement the pattern.

Design patterns are programming language-independent. Design patterns can be implemented in whatever language or framework you choose.

## The Iterator Design Pattern

Programming languages have different data structures. Some are
primitives (boolean and integers) and some are made up of combining one or more
primitives (collections such as arrays and objects). The challenge here is that
programmers want a consistent pattern that they can use to traverse any collection
 irrespective of what the collection is (sets, maps, arrays, lists, trees).
 While traversing the collection, programmers also want to be able to do two things:

1. Get the item at the current point in the traversal
2. Determine whether the traversal has reached the end of the collection or not

However the traversal is done whether from the first item to the last item or the reverse is not the main concern. The main concern is to have a consistent pattern that will achieve aim 1 and aim 2 for any collection.

As a JavaScript programmer, you may wonder why this is a challenge because you
can run a _for...index_ loop to achieve this. Have it in mind that design
patterns are language-independent. In other programming languages, collections
such as linked lists and trees exist, and they do not have indices like
JavaScript arrays. The iterator design pattern[^1] proposes a solution to resolve
this so that for any collection in JavaScript, you can traverse through its items without the need of an index.

The iterator design pattern states that for its implementation, collections should
1. be able to produce an object (called the **iterator**)
2. have a method (you can call it `getCurrent`) on the iterator object
3. make the `getCurrent` method return an object when executed

The object returned from the `getCurrent` method when executed should have two fields. You can call them
1. `isDone`
2. `curentItem`

`isDone` holds a boolean value that denotes if the traversal has reached the end of the collection and no more traversal can be done. `currentItem` holds the value of item in the collection that the traversal process is at.

A collection that implements the iterator design pattern is called an **iterable**.

## JavaScript's Implementation of the Iterator Design Pattern

JavaScript implements the iterator pattern via the iteration protocol [^2] on
some data structures (arrays, strings, maps). This makes these data structures
iterables.

For any collection (or data structure) to be an iterable in JavaScript, it must
be able to produce an iterator object, just as the iterator pattern has
demanded. Data structures in JavaScript such as arrays, strings and maps, have a
function property that when called, returns an iterator object. The name of that
function property is `Symbol.iterator`, and it can be executed on an array
instance in the same way that `push` can be executed on an array instance.

```js
const arr = [1, 2, 3];

console.log(typeof arr["push"]); // "function"
const pushReturnVal1 = arr.push(4); // pushReturnVal1 = 4
const pushReturnVal2 = arr["push"](5); // pushReturnVal2 = 5

console.log(typeof arr[Symbol.iterator]); // "function"
const iteratorObject = arr[Symbol.iterator]();
```

Drawing from the iterator design pattern, `iteratorObject` in the code snippet
above should have a method that when called, will return an object with two
fields. `iteratorObject` has that method and it is called `next`.

```js
console.log(iteratorObject.next()); // { value: 1, done: false }
console.log(iteratorObject.next()); // { value: 2, done: false }
```

Isn't this exciting? It rightly returns an object with two fields - `value` and
`done`. `value` represents the item at the current position of the traversal
and `done` holds a boolean value that connotes if there are
any more items to return (`true`) or not (`false`).

If `iteratorObject.next` in the code above is
executed a total of five times, the return value of the _next_ function for any run
after the fifth run will be `{ value: undefined, done: true }`. The behaviour of
this `Symbol.iterator` property of arrays, strings and maps in JavaScript is
what makes it possible for developers to use the `for...of` syntax to iterate over or traverse
through them.

## Implementing the Iterator Pattern on a JavaScript Object

One of the amazing things about the iteration protocol in JavaScript is that the
pattern can be applied to other data structures. If an object is created and

1. it has a `Symbol.iterator` method which when called, returns an object
   with a `next` method and
2. the `next` method returns an object with a `done` and a `value`
   property and
3. the `done` and `value` properties align with the demands of the iterator
   pattern

then that object is an iterable.

In the following code snippet, we try to make a non-array object an iterable.

```js
const obj = {
  // A Symbol.iterator method
  [Symbol.iterator]: function () {

    let num = 0;

    // The function returns an object with a function called `next`
    return {
      next: function () {
        num = num + 1;

        // `next` returns an object with the fields `done` and `value` when called
        return {
          // both `done` and `value` are optional, but one of them must be returned
          done: num > 3 ? true : false,
          value: num > 3 ? undefined : num,
        };
      },
    };
  },
};

// 1. using the iterator (for...of)
for (const num of obj) {
  console.log(num); // 1 2 3
}

// 2. using the spread operator
console.log(...obj); // 1 2 3

// 3. using the iterator object
let iterator = obj[Symbol.iterator]()

while (true) {
  const container = iterator.next()
  console.log("value:", container.value, "done:", container.done);
  // value: 1 done: false
  // value: 2 done: false
  // value: 3 done: false
  if (container.done == true) {
    break
  }
}
```

{{< figure caption="Iterator Design Pattern Animation" align=center src="../images/iterator-design-pattern-animation.gif" >}}


The object `obj` created is not an array, but because you have implemented the
iterator pattern on it, you can iterate over it using _for...of_ and also use the
spread operator on it. It is an iterable. This would not be possible if you
had not implemented the iterator pattern on it. These contructs (_for...of_ and
the spread operator) cause JavaScript to call the `next` method on the iterable
continually (internally, as you don't see the explicit call) until the method
returns a value of `false` for the `done` field. For each call to `next`, the
value of `value` is returned to the client and you see it logged to the console.

With the iterator pattern implemented, you can use the _for...of_ or the iterator
object itself to itereate over a range of values in a collection. The algorithm
with which the iterator uses to return values is left to the implementor of the
pattern. The main concern is having a consistent iterface that clients can rely
on to retrieve values from the collection while traversing it.

[Blog post image - Business illustrations by Storyset](https://storyset.com/business)

## References

[JSJ 468: The case for JavaScript iterators, part 1](https://topenddevs.com/podcasts/javascript-jabber/episodes/jsj-468-the-case-for-javascript-iterators-part-1)

[^1]: [Refactoring Guru - Iterator Design Pattern](https://refactoring.guru/design-patterns/iterator)
[^2]: [Iteration protocols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)
