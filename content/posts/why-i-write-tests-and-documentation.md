---
title: "Why I Write Tests and Documentation and Why You Should Too"
date: 2024-03-02T11:03:55+01:00
author: "Orim Dominic Adah"
description: "With automated testing, you can avoid repeated manual verification and testing of your code"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["testing", "documentation"]
---

In November 2023, I was at the [API Conf Lagos](https://sessionize.com/api-conf-lagos-2023/) meetup. One of the speakers, [Toni Alaribe](https://twitter.com/tonialaribe), asked members of the audience to signify by show of hands if they write tests for HTTP APIs they build. Myself and a couple of other people raised hands. He asked members of the audience to signify again by show of hands if they write API documentation, update the documentation with the latest changes and also keep a changelog of the changes. Only two people raised hands and I was one of them.

He was surprised and asked why we did it. To him, it was unusual. It was arduous for anyone to dedicate time and energy to doing these things, even though they are important. I let him know that I did it for my peace of mind.

What makes writing tests and keeping documentation updated give me peace of mind is what this article is about.

> Postman keeps a changelog of changes in an API documentation. Toni did not know at that time.

## Why I write tests

### 1. That I may avoid the suffering in repeating myself

I once watched a talk - [Automated Testing JavaScript](https://youtu.be/JqSoatUePdU?t=270), in which Dr. Venkat Subramaniam said, to paraphrase:

> *..having to test the changes in your codebase by starting the application and running through the flow, end-to-end, every time you make a change, is you repeating yourself..*

I had never seen it from that angle. DRY to me was about bundling a set of operations in a function to avoid rewriting the set of operations again when needed. With automated testing, I can implement a feature or fix a bug and then write an automated test that would ensure that my contribution works as expected.

How many steps in the feature or bug fix can the human mind remember, monitor and verify, time and again for every new change in the codebase? What about the steps that are in this feature/bug fix but also connected to other features or bug fixes? Best of luck trying to verify that all these work as they should by manually testing them all every time there is a change in the codebase.

Do what you know how to do best - write code, write tests and have fun!.

![Harold the manual tester](../images/testing-and-documentation-harold-suffering.jpg#center)


### 2. That I may have automated allies

Well-written automated tests help keep contributions locked to specification and notify if a specification is broken.

{{< x user="orimdominic_" id="1759179956251238890" >}}

In a codebase with many contributors, it is normal and expected that code will be changed by those who did not author it. Automated tests are allies as well as guardrails. They help prevent too many cooks from *spoiling the broth*.

{{< x user="orimdominic_" id="1558432897421418498" >}}

If a code change breaks something, your automated allies will tell you. They will not only tell you that something is broken, they will also tell you exactly what got broken and where it is broken. Isn't that amazing?

One only needs to write the tests once and they become a guardrail and an ally for every contributor in the codebase forever. That is an efficiency greater than 100% if you ask me. Enough to give peace of mind don't you think?

## Why I write documentation

### 1. That production may be truly complete

There is a quote in the field of Economics that goes like this

> *Production is not complete until the goods get to the final consumer.*

As a developer building API endpoints or packages or libraries, production is not complete until the intended users of the product can use what has been produced. How can they use it if there is no documentation that demonstrates how to use it?

Up-to-date documentation makes the product useful. Invalid documentation or no documentation makes the product useless. Who wants to make useless products? Not me! Not you either!

[Shalvah](https://twitter.com/theshalvah), the creator of the popular [RemindMeOfThis](https://twitter.com/remindme_ofthis) Twitter bot, made a free course on [API Documentation for Developers](https://shalvah.teachable.com/p/api-documentation-for-developers) which I highly recommend.

### 2. Selfish interests

Imagine that you are taking a rest after work. Your phone rings. It is a call from a colleague. There are no bugs anywhere and all is well but s/he does not know how to plug in to the work that you have done because there is no documentation.

I want to have uninterrupted free time. I want to do work once and be abstracted from it while the work I did runs well by itself. Is that not what we do as developers most of the time? We write code that instructs computers to do things so that people can be free to do more meaningful things?

When I build a product for use and I do not write documentation, I am tying myself to what I have built. I am still part of the product. Whenever the product is needed to work, I will be called upon. Writing good documentation solves that problem and I can have my free time. Call me selfish!

## Conclusion

I have a hunch that developers do not write tests because it is hard, and it is hard because the code they wrote is hard to test. The good part is, writing testable code is a sign of good design. You can read the [Design With Testing in Mind](https://6figuredev.com/testing/design-with-testing-in-mind/) article by Clayton Hunt to see what I mean.

Writing documentation improves your communication skills as a developer and as a person. In writing documentation, you invest your resources in trying to see things from the point of view of your listener/reader and you construct what you have to say so that they can understand it properly. If you are well-understood when you communicate, you will feel fulfilled.
