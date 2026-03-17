---
title: "Dependency Injection in JavaScript"
date: 2023-02-22T05:36:45+01:00
author: "Orim Dominic Adah"
description:
  "Dependency Injection helps make our applications less tightly-coupled and
  easily testable. How is it done in JavaScript"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["dependency injection", "javascript", "software design principles"]
cover:
  image: "../posts/images/dependency-injection-in-javascript.jpg"
  alt: "Dependency Injection in JavaScript blog header image"
  caption: ""
  relative: false
---

In building a house, after the architecture of the house has been drawn and
accepted, everything built must fit the drawn architecture. A major change along the
way may result in a complete tear-down and rebuild from scratch process. Once the building has been built with cement, it cannot be changed to wood. Building physical structures is a rigid process.

Building software is different. Software, unlike physical structures, is expected to be flexible. If the requirements for a software being built changes along the way, it is expected that the changes are implemented without
the need to tear it all down. New features, bug fixes, a change in the design system or database, all modify software without the need to tear it all down.

## What is a Dependency?

If we have a function called `authenticateGoogleUser` in a part of our codebase,
and its function is to

1. verify if a user's Google OAuth token is valid
2. get the user from the application's database
3. create and return a JWT from the user's database data

the function can be written in the manner described in the snippet below:

```js
// authenticate-google-user.js

const createJwt = require("jwt");
const fetchUserFromDbOrFail = require("db.js");
const verifyGoogleOAuthToken = require("gauth.js");

async function authenticateGoogleUser(token) {
  try {
    const { email } = await verifyGoogleOAuthToken(token);

    const user = await fetchUserFromDbOrFail({ email });

    const jwt = createJwt(user.id);

    return jwt;
  } catch (error) {
    // do something with the error
  }
}
```

`authenticateGoogleUser` _depends_ on `verifyGoogleOAuthToken`, `fetchUserFromDbOrFail` and `createJwt` to function. Each of the three functions that `authenticateGoogleUser` depends on is called a *dependency*.

## The Need for Dependency Injection

`authenticateGoogleUser` has been written in a way that makes it tightly-coupled
to its dependencies. This means that if `authenticateGoogleUser` needs to fetch
a user from a different store, or it is decided that `authenticateGoogleUser`
should no longer return a JWT but an OAuth2 token, we may have to tear down
`authenticateGoogleUser` and rewrite it.

Another issue here is testing `authenticateGoogleUser`. `verifyGoogleOAuthToken` and `fetchUserFromDbOrFail` are both functions that require making a request to services outside of the code environment, that is, Google OAuth and the database, respectively. It will be near impossible, if not impossible, to run a unit test for `authenticateGoogleUser` without mocking the dependencies in order to prevent making requests to the external services.

With dependency injection, dependencies are _injected_ into their dependents (`authenticateGoogleUser` in this case) as arguments. How does this look in JavaScript?

## Implementing Dependency Injection

In JavaScript, functions can be passed into other functions as arguments the same way that strings and objects can be passed into functions as arguments. Using the [default parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters) feature of JavaScript, the default dependencies of a code component (class or function) can be passed into the function by default.

Let's refactor `authenticateGoogleUser` to implement dependency injection.

```js
// authenticate-google-user.js

const createJwt = require("jwt");
const fetchUserFromDbOrFail = require("db.js");
const verifyGoogleOAuthToken = require("gauth.js");

async function authenticateGoogleUser(
  token,
  verifyToken = verifyGoogleOAuthToken,
  fetchUserOrFail = fetchUserFromDbOrFail,
  createToken = createJwt
) {
  try {
    const { email } = await verifyToken(token);

    const user = await fetchUserOrFail({ email });

    const authToken = createToken(user.id);

    return authToken;
  } catch (error) {
    // do something with the error
  }
}

const authToken = await authenticateGoogleUser("a_token")
```

From the code snippet above, the dependencies of `authenticateGoogleUser` have been _injected_ as arguments into it. With the default parameters feature of JavaScript, we can execute `authenticateGoogleUser` by passing only the `token` argument. `verifyToken`, `fetchUserOrFail`, and `createToken` will be `verifyGoogleOAuthToken`, `fetchUserFromDbOrFail` and `createJwt` by default, respectively.

## How does Dependency Injection Influence Tight-coupling?

The manner in which `authenticateGoogleUser` has been re-written makes it more
flexible. If new software requirements demand that the user should be fetched
from another service or another database or that it should retuen an OAuth token and not a JWT,
developers can rewrite `authenticateGoogleUser` as the code snippet below with ease.

```js
// authenticate-google-user.js

const createOAuthToken = require("oauth.js");
const verifyGoogleOAuthToken = require("gauth.js");
const fetchUserFromServiceOrFail = require("users-service.js");

async function authenticateGoogleUser(
  token,
  verifyToken = verifyGoogleOAuthToken,
  fetchUserOrFail = fetchUserFromServiceOrFail,
  createToken = createOAuthToken
) {
  try {
    const { email } = await verifyToken(token);

    const user = await fetchUserOrFail({ email });

    const authToken = createToken(user);

    return authToken;
  } catch (error) {
    // do something with the error
  }
}
```

It is evident how clean and easy this change in specification was implemented. The content of `authenticateGoogleUser` remains the same, but it still does what it is required to do.

## How does Dependency Injection Influence Testing?

In the same vein as reducing tight-coupling, developers can swap the values of `verifyToken` and `fetchUserOrFail` for functions that do what they are testing for. They do not have to make requests outside of the code environment to test `authenticateGoogleUser` or use mocks provided by the testing framework.

```js
test("it returns an auth token", async () => {
  const googleToken = "123456";

  const verifyToken = (token) => {
    return {
      name: "John Doe",
      email: "johndoe@mail.com",
    };
  };

  const fetchUserOrFail = (data) => {
    return {
      id: "abc_123",
      email: "johndoe@mail.com",
    };
  };

  const authToken = await authenticateGoogleUser(
    googleToken,
    verifyToken,
    fetchUserOrFail
  );

  expect(authToken).to.be.a.string();
});
```

`createToken` in the test above will have the default value in the
`authenticateGoogleUser` function definition.

## Conclusion

Software can be built in such a way that its component parts (functions and
classes) are not tightly-coupled and can be easily changed or replaced without
breaking changes going undetected. For reducing tight-coupling and making sure
that breaking changes are easily detected, two software design principles -
dependency injection and testing - can be used, respectively.
