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

Binary search is one of the most popular algorithms for searching for a number within a sorted list of numbers. It has a time complexity of *O(log N)*, which means that its ability to find a number in a sorted list is minimally affected by the length of numbers in the list - for each turn, the length of the list to search in reduces by half, so there's less work to do.

## Binary Search - Analogy

The binary search algorithm is similar to how you search through a numbered book to get to a page. First, you open up to a random page. If the page number is greater than that of the page you are looking for, you search leftwards, else, you search to the right. You repeat this procedure until you find the page with the number you are looking for.

Two things stand out:
1. the page numbers are **sorted** so you can know to search left or search right
2. one page number does not appear twice - every number is unique
3. the number of pages is **finite** (has a definite end)

In binary search, you ask the computer to do something similar:
1. start from a midpoint in the collection
2. evaluate if the midpoint number is greater than or less than the number being searched for
3. search forward or backward based on the result of the evaluation until the number is (not) found

The algorithm is popularly called **divide and conquer** because for each turn where it does not find what it is searching for in a comparison, it focuses on one half of the collection, ignoring the other half; dividing the items by half after each evaluation.

## Binary Search - Pseudocode

Situations requiring binary search are usually worded in the following manner:
> Given a list of numbers **sorted** in ascending order, **find** out if the number *target* exists in the list. If it does, return the index where it was found, otherwise, return -1.

How do you go about solving this with code?

### 1. Handle Edge Cases
It is possible that the list contains no numbers one number. In that case, it will be efficient to exit early. To do that, you check if the length of the list is zero and exit with a return value of -1.

```js
if (list.length == 0) return -1
```

> The constraints of the question may highlight that the list will always have at least one number. If that is the case, this stage is not necessary and can be omitted.

### 2. Establish Boundaries
At this point, you want to establish the bounds within which you can search.

 You cannot go beyond the last item in the list found at `list[list.length - 1]`, and the first item in the list found at `list[0]`. With that, you have your upper bound index (`ubi`) and your lower bound index (`lbi`) respectively.

```js
lbi = 0;
ubi = list.length - 1;
```

### 3. Search Iteratively

You begin to search through the list the way you would search for a page in a book. When you search through a book for a page, you first open to a random page. In binary search, this means accessing the item at the middle of the list first, not just a random number in the list as you will do in real life. How do you get to the middle of the list?

```
mi = round_down((upperIndex + lowerIndex) / 2)
```

The sum of the boundaries divided by two and then rounded down is the middle index (`mi`) of the list. It is rounded down so that the upper bound is not overstepped. Overstepping the upper bound can lead to accessing an index higher than the maximum index of the list, and that would cause an *index out of range error*.

The operations repeated in this search start from accessing a page. You would need a loop to perform operations repeatedly.

```js
...
while (condition to terminate the loop) {
  // accessing a middle index value
  mi = round_down((ubi + lbi)/2)
  cVal = list[mi]
}
```

### 4. Compare Values

`cVal` is the current value of the number at `mi`. With `cVal`, you can determine if you should search towards the left of the list, towards the right or exit because `target` has (not) been found.

```js
...
while (condition to terminate the loop) {
  mi = round_down((upper + lower)/2)
  cVal = list[mi]

  if (target == cVal) {
    return mi // (1)
  }

  if (target > cVal) {
    lbi = mi + 1 // (2)
  }

  if (target < cVal) {
    ubi = mi - 1 // (3)
  }
}
```

The trick here is to compare `target` with `cVal` and let the direction of the comparison operators (`<`, `>` and `=`) tell you what to do.

If `target` is equal to `cVal`, you have found the number. You can return the index (1).

If `target` is greater than `cVal`, then the number you are looking for is towards the right of `mi` (and the comparison operator guides you by pointing towards the right).

To move to the right, you increase the lower bound index (`lbi`) by setting it to the index just right after the middle index; that is - `mi + 1`, because you have ascertained from the first comparison that `target` is not equal to the current `cVal` and from the second comparison, that it is lower than `cVal`.

If `target` is less than `cVal`, then the number you are looking for is towards the left of `mi`; every number from `mi` to `hbi` doesn't count anymore. The new `hbi` will be the index just before `mi`, which is `mi -1`.

### 5. Exit the loop

For each run of the loop where you did not find *target*, it is either `lbi` is increased, tending towards `ubi`, or the `ubi` is reduced, tending towards `lbi`. The list has been fully searched through when `lbi` becomes higher than `ubi`. The condition to keep running the loop is then `while (lbi <= ubi)`.


```js
while (lbi <= ubi) {
  mi = round_down((ubi + lbi)/2)
  cVal = list[mi]

  if (target == cVal) {
    return mi // (1)
  }

  if (target > cVal) {
    lbi = mi + 1 // (2)
  }

  if (target < cVal) {
    ubi = mi - 1 // (3)
  }
}
```

## Binary Search - JavaScript

Here's a JavaScript implementation of binary search

```js
/**
* @param {number[]} list the list of numbers sorted in ascending order
* @param {number} x the number whose index we are searching for
*/
function findIndex(list, target) {
  if(list.length == 0) return -1

  let lbi = 0
  let ubi = list.length - 1

  while(lbi <= ubi) {
    const mi = Math.floor((lbi + ubi)/2)
    const cVal = list[mi]

    if (target == cVal) {
      return mi
    }

    if (target < cVal) {
      ubi = mi - 1
    }

    if (target > cVal) {
      lbi = mi + 1
    }
  }

  return -1
}
```

## Binary Search - Golang

Here's an implementation of binary search in Golang

```go
func findIndex(list []int, target int) int {
  if len(list) == 0 && list[0] == x {
    return 0
  }

  if len(list) == 0 && list[0] != target {
    return - 1
  }

  lbi := 0
  ubi := len(list) - 1

  for lbi <= ubi {
    mi := (lbi + ubi)/2
    cVal = list[mi]

    if target == cVal {
      return mi
    }

    if (target < cVal) {
      ubi = mi - 1
    }

    if (target > cVal) {
      lbi = mi + 1
    }
  }

  return -1
}
```

## Conclusion

It's great to have you still reading at this point. It implies that you have gone through the article. Here are a couple of things you may want to try for practice.

1. How do you update the algorithm to fit situations where the numbers in the list are in descending order?

2. How do you update the algorithm to fit situations where there are no numbers in the list, but objects such as `person.age` and you have to find the person with a certain age *x*

3. Can you try [Question 278](https://leetcode.com/problems/first-bad-version/description/) on LeetCode to test your knowledge?
