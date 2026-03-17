---
title: "A Guide to Understanding Binary Search"
date: 2024-06-01T22:25:07+01:00
author: "Orim Dominic Adah"
description: "Binary Search - the algorithm and its implementation in JavaScript and Golang"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["binary search", "algorithms", "golang", "javascript"]
---

In working with collections (lists, queues, maps) of data, you usually have to perform one or more of the following operations on the collection: accessing, searching, appending, inserting, replacing/swapping, deleting and sorting.

Binary search is one of the most popular algorithms for searching for a number within a sorted list of numbers. It has a time complexity of *O(log N)* which means that its ability to find a number in a sorted list is minimally affected by the length of numbers in the list.

## Binary Search - Analogy

The binary search algorithm is similar to how you search through a numbered book to get to a page. First, you open up to a random page. If the page number is greater than that of the page you are looking for, you search leftwards, else, you search to the right. You repeat this procedure until you find the page with the number you are looking for.

Two things stand out
1. the page numbers are **sorted** so you can know to search left or search right
2. the number of pages is **finite** (has a definite end)

In binary search, you ask the computer to do something similar:
1. start from a midpoint in the collection
2. evaluate if the midpoint number is greater than or less than the number being searched for
3. search forward or backward based on the result of the evaluation until the number is found

The algorithm is popularly called **divide and conquer** because each time it does not find what it is searching for in a comparison, it focuses on one half of the collection, ignoring the other half; dividing the items by half after each evaluation.

## Binary Search - Pseudocode

Situations requiring binary search are usually worded in the following manner:
> Given a list of numbers **sorted** in ascending order, **find** out if the number *x* exists in the list. If it does, return the index where it was found, otherwise, return -1.

How do you go about solving this with code?

### 1. Handle Edge Cases
It is possible that the list contains only one number. If it does, it would be efficient to exit early. To do that, you check if that single number is the number you are looking for *(x)*. If it is, you return the index of the number *(usually 0)*, else, you return *-1*.

```js
if (list.length == 1 and list[0] != x) return -1
if (list.length == 1 and list[0] == x) return 0
```

### 2. Establish Boundaries
Having handled that edge case, you can move on to searching through lists that have more than one number. To do this, you want to first establish the bounds within which you can search.

 You cannot go beyond the last item in the list found at `list[list.length - 1]` and the first item in the list found at `list[0]`. With that, you have your upper index bound and your lower index bound respectively.

```js
lowerIndex = 0;
upperIndex = list.length - 1;
```

### 3. Search

You begin to search through the list the way you would search for a page in a book. In searching through a book, you first open to a random page. In binary search, you access the item at the middle of the list first, not just a random number in the list. How do you get to the middle of the list?

```
midIndex = round_down((upperIndex + lowerIndex) / 2)
```

The sum of the boundaries divided by two and then rounded down is the middle index of the list. It is rounded down so that the upper bound is not overstepped. Overstepping the upper bound can lead to accessing an index higher than the maximum index of the list, and that would cause an *index out of range error*.

The operations repeated in this search start from accessing a page. You would need a loop to perform operations repeatedly.

```js
...
while (condition to terminate the loop) {
  // accessing a middle index value
  midIndex = round_down((upperIndex + lowerIndex)/2)
  midVal = list[midIndex]
}
```

### 4. Compare Values

`midVal` gives you the number at `midIndex`. With `midVal`, you can determine if you should search towards the left of the list, towards the right or exit because *x* has been found.

```js
...
while (condition to terminate the loop) {
  midIndex = round_down((upper + lower)/2)
  midVal = list[midIndex]

  if (x == midVal) {
    return midIndex // (1)
  }

  if (x > midVal) {
    lower = midIndex + 1 // (2)
  }

  if (x < midVal) {
    upper = midIndex - 1 // (3)
  }
}
```

If `x` is `midVal`, you have found the number. You can return the index(1).

If `x` is greater than `midVal`, then the number you are looking for is towards the
right. You have to increase the next value of `round_down((upperIndex + lowerIndex)/2)` (also known as `midIndex`) in the next loop so that it can point further to the right.

In trying to achieve this, you cannot increase `upperIndex` because it is the
upper bound and it should not exceed the maximum list index. You can
increase lower though. You set it to `midIndex + 1` because the number you are
looking for is beyond `midIndex`. (2)

If `x` is less than `midVal`, then the number you are looking for is on the left of
the list. You have to reduce the next value of `midIndex` (in the next loop) so that it can point leftwards.

In doing this, either `lowerIndex` or `upperIndex` must be reduced. You cannot reduce `lowerIndex` because you may end up reducing it to below 0 and then have an *index out of range error*. What you do is reduce `upperIndex` by 1 instead. (3)

### 5. Exit the loop

For each run of the loop where you did not find *x*, it is either `lowerIndex` is increased, tending towards `upperIndex`, or the `upperIndex` is reduced, tending towards `lowerIndex`. The list has been fully searched through when `lowerIndex` becomes higher than `upperIndex`. The condition to keep running the loop is then `while (lowerIndex <= upperIndex)`.


```js
while (lower <= upper) {
  midIndex = round_down((upperIndex + lowerIndex)/2)
  midVal = list[midIndex]

  if (x == midVal) {
    return midIndex // (1)
  }

  if (x > midVal) {
    lower = midIndex + 1 // (2)
  }

  if (x < midVal) {
    upper = midIndex - 1 // (3)
  }
}
```

## Binary Search - JavaScript

Here's a JavaScript implementation of binary search

```js
/**
* @param {number[]} list the list of numbers sorted in ascending order
* @param {number} x the number for the index we are searching for
*/
function findIndex(list, x) {
  if (list.length == 1 && list[0] == x) return 0
  if (list.length == 1 && list[0] != x) return -1

  let lowerIndex = 0
  let upperIndex = list.length - 1

  while(lowerIndex <= upperIndex) {
    midIndex = Math.floor((lowerIndex + upperIndex)/2)
    midValue = list[midIndex]

    if (midValue == x) {
      return midIndex
    }

    if (midValue > x) {
      lowerIndex = midIndex + 1
    }

    if (midValue < x) {
      upperIndex = midIndex - 1
    }
  }

  return -1
}
```

## Binary Search - Golang

Here's an implementation of binary search in Golang

```go
func findIndex(list []int, x int) int {
  if len(list) == 0 && list[0] == x {
    return 0
  }

  if len(list) == 0 && list[0] != x {
    return - 1
  }

  lowerIndex := 0
  upperIndex := len(list) - 1

  for lowerIndex <= upperIndex {
    midIndex := (lowerIndex + upperIndex)/2
    midValue = list[midIndex]

    if midValue == x {
      return midIndex
    }

    if midValue > x {
      lowerIndex = midValue + 1
    }

    if midValue < x {
      upperIndex = midValue - 1
    }
  }

  return -1
}
```

## Conclusion

It's great to have you still reading at this point. It suggests that you have gone through the article. Here are a couple of things you may want to try for practice.

1. How do you update the algorithm to fit situations where the numbers in the list are in descending order?

2. How do you update the algorithm to fit situations where there are no numbers in the list, but objects such as `person.age` and you have to find the person with a certain age *x*

3. Can you try [Question 278](https://leetcode.com/problems/first-bad-version/description/) on LeetCode?
