---
title: "Single-Letter Identifiers in Loops"
date: 2024-03-24T12:00:11+01:00
author: "Orim Dominic Adah"
description: ""
draft: false
showToc: false
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["clean code", "programming"]
---

I recently developed a practice of using single-letter identifier names in code I write. This practice is quite contrary to my principle for writing clean code. I aim to make readers understand the code I write without thinking too hard. However, I have two points of view to support this practice.

> *identifier* is the generic term for variable names and names of constants. I use the term *identifier* and not *variable name* because *identifier* encompasses variable names and constant names.

## Loops already have a collection name

Take for example the code snippet below

```js
const prices = products.map((p) => p.price)
```

Although the identifier in the loop is a letter `p`, I doubt that readers will falter in knowing that `p` signifies `product` because `.map` loops over products, therefore, every item in the loop is a product.

## This is fine for typed programming languages

If the code is written in a typed programming language such as Golang or TypeScript, this practice should not be an issue. `p` will be typed as an item in the `products` collection, making it easy to figure out.