---
title: "How I Validated for Specific URLs in JavaScript"
date: 2023-08-18T07:02:31+01:00
author: "Orim Dominic Adah"
description:
  "In this article, you will find how to make sure that users submit the type of
  URLs that you expect"
draft: false
showToc: false
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["validation", "URL", "javascript"]
cover:
  image: "../posts/images/how-i-validated-for-specific-urls-in-in-javascript.jpg"
  alt: "URL validation JavaScript header image"
  caption: ""
  relative: false
---

In a pet project that I worked on recently, one of the requirements was to allow
users to submit the URL to their Facebook social media profile. This article
describes how I ensured that only Facebook profile URLs were submitted.

JavaScript was used but it does not matter as much as the algorithm used; and
that algorithm is described here.

```js
function validateUrl(url, expectedOrigin) {
  const urlObject = new URL(url);
  const originPattern = new RegExp(expectedOrigin.toLowerCase() + "$");
  const isValid = urlObject.origin.toLowerCase().match(originPattern);

  if (!isValid) {
    throw new Error("Invalid URL");
  }

  return `${urlObject.origin}${urlObject.pathname}`;
}

console.log(validateUrl("https://facebook.com/hi-there", "facebook.com")); //https://facebook.com/hi-there
console.log(validateUrl("https://handbook.com/hi-there", "facebook.com")); // throws error
```

{{< figure caption="Components of a URL. Source - MDN" align=center src="../images/components-of-a-url.png" >}}

The goal of the algorithm is to extract the domain name or the origin of the
submitted URL and verify that it is the same as the expected origin. Since
JavaScript is being used, we can use the
[URL API](https://developer.mozilla.org/en-US/docs/Web/API/URL_API) to extract
the origin. Using RegEx or any string comparator, we can verify if the origin of
the URL submitted matches the one that is expected by checking that it ends with
the expected origin.

RegEx was used for this article because it cuts across many programming
languages. In JavaScript, we can use
[endsWith](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/endsWith)
.

Checking that it _ends with_ is for cases where a URL like _web.facebook.com_ is
submitted. _web.facebook.com_ is valid for the use case.

This method can be used on the frontend and the backend with Node.js. The URL
API exists in both environments.

Cheers!
