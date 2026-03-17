---
title: "My First Open Source Contribution to Remotion"
date: 2023-02-11T08:31:23+01:00
author: "Orim Dominic Adah"
description:
  "How I made my first open source contribution to Remotion - An open source
  video-making program built with React"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["open source", "remotion"]
cover:
  image: "../posts/images/my-first-open-source-contribution-to-remotion.png"
  alt: "Design JavaScript code with tests in mind"
  caption: ""
  relative: false
---

## What is Remotion?

[Remotion](https://remotion.dev) is an open source video-making program built
with [React](https://reactjs.org/). Remotion enables users to create MP4 videos
using React. At the time of writing this blog post, the project is recorded to
have been used by over 800 developers on GitHub and it has over 15,000 stars.

Remotion has been used to make product demos, tutorials and clips for conference
talks. Remember the popular yearly
[GitHub unwrapped](https://githubunwrapped.com) videos that show an animated
display of developers' GitHub history for the year? Those videos were built with
Remotion!

If you are looking forward to making videos, try Remotion. Check out the
[documentation](https://www.remotion.dev/docs/) or get your hands dirty quickly
by using any of the [existing templates](https://www.remotion.dev/templates).

## The Issue - Open Remotion project in VS Code from the preview

My mentor hit me up and asked me to check out a certain issue on GitHub. It was
an issue titled ["Allow to open project in VS Code or other editor from the preview"](https://github.com/remotion-dev/remotion/issues/1561).
The issue requested that Remotion projects being previewed in the
browser should have the option of being opened in a code editor from the
browser.

## Setting Up

My first step was to understand the problem context. I achieved this by reading
the comments on the Github issue and also the code and comments on the associated
links. After getting a solid understanding of the issue, I read the contribution
guidelines and the code of conduct documentation to know what was required of
contributors and contributions. I was a first-timer to Remotion and the Remotion
codebase and fortunately, the documentation was welcoming, concise and helpful.

I delved into the codebase to study the existing code that was referenced in the
issue. It was going to serve as the foundation for the code that I was going to
write. I took a look at how the UI code mapped to the UI that I was to
work with. After getting a good grasp, I wrote down my algorithm for how I felt
that the issue could be resolved and then I went on to do research on
code-related solutions.

## Implementing the solution

First, I set up the UI on the navigation bar for the feature. I chose to do this
first because there was a template for navigation bar items set up in the
project and I could reuse it. I also did this first because it would be easier
to use UI elements to trigger the code that would open the the editor as opposed
to triggering it via code while testing my work as I progressed.

Once that was set up, I studied the code that was referenced in the GitHub
issue. This part was interesting because it was code from
[create-react-app](https://github.com/facebook/create-react-app), a different
codebase. create-react-app has this feature that enables code editors to be
opened from the browser when an error is encountered while running the
application.
[RohovDmytro referenced it](https://github.com/remotion-dev/remotion/issues/1561#issuecomment-1346870575).
I studied it, integrated it and tweaked it a bit for it to work. It was the
first time I used Node's `child_process` module in a production application. I
used it to run the terminal command that opens the code editor.

## Submitting and Implementing Feedback

The setup worked well for the VSCode editor but had issues when I tested for
Sublime Text. I made a PR and gave my report on my implementation. I received
constructive feedback from Johnny Burger and also suggestions on implementations
I considered to be gray areas. For example, what happens when the user has no
code editor? What happens if an error occurs in trying to open the code editor?

I implemented the suggestions, fixed the Sublime Text bug by changing the
terminal command that was used for it (`sublime` to `subl`) and also made code
reusable. One other part I added was the ability to check if a path to be opened
by an editor was a file or folder and use the appropriate command for it. If it
was a file, it should open the code editor and point to the exact line.

In all of these, there were times at different points where I tried to solve a
problem but my algorithm failed. I had to tweak it a couple of times to get it
to work. I made a PR and added notes to the PR about my implementation.

![Feedback on pull request](../images/feedback-on-first-remotion-pull-request.png#center)

## What I have learnt

### You don't need to have all the answers

This is one thing I feel holds people back from contributing to open source.
They feel that they need to know the solution before they contribute. No.

> Solving problems that you already know how to solve does not increase the
> breadth of your experience.

You can pick an issue, do your research, ask questions and suggest a solution.
Your solution does not have to be 100 percent correct the first time. There will
be maintainers who would review it and make suggestions if need be. One has a
lot more to benefit from solving problems like these because in the course of PR
discussions, developers are also networking and learning concepts from the views
and experience of other developers.
