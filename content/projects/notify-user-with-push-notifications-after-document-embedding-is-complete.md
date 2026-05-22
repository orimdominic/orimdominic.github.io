---
title: "Notify User With Push Notifiations After Document Embedding Is Complete"
date: 2026-05-22T18:12:51+01:00
author: "Orim Dominic Adah"
description: "A demo project where I use push notifications in Go to notify a client after their document embedding process is completed"
draft: true
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["RAG", "Go", "Push Notifications"]
---

I built this application as a response to a question from a WhatsApp group I'm in. A groupie asked for recommendations to solve the issue he had:

```txt
When a user uploads a document, I parse and embed the file
so that the LLM can have the same information in the file.

After uploading the file, the embedding process is queued
in a background task, and when it is done, it updates a
database table row with the status of the task - success,
processing or failed.

I created another endpoint that the frontend polls for the
status of the embedding process task.

I feel that this is not very necessary and this process is
not optimal. Is there a better way to do this?
```

I pulled out a pen and paper to sketch a solution. After considering the different client-server communication protocols available, I chose to use push notifications because:

- It is free
- It is not as resource intensive as server-sent events and web sockets

I responded with my solution, but I felt it wasn't enough - Scott Adams

> Ideas are useless, execution is everything.

> In theory, theory and practice are the same thing. In practice, they are not.

I decided to implement my solution to see if what I designed actually works and that's how [sally](https://github.com/orimdominic/sally) was born. sally is a full-stack application built with:

- Go
- Node.js
- Genkit
- Redis

![Sally Architecture](../images/sally-arch.png)

## How does sally work?

The user sends a `.pdf` document over to the server via a HTTP request. When the file is saved successfully, two operations are triggered:

- A response is sent to the user to end the request/response cycle
- The saved file is sent to a background task for RAG indexing

The user also sends their web push subscription keys to the server alongside the `.pdf` document.

### How is the indexing done?

During indexing, text is extracted from the document and split into chunks by the Go server. These chunks are sent to a Node.js server via HTTP for embedding, and the embeddings returned are stored using Genkit's localvector store.

### How is the user notified?


### What constraints did I work under?

Embedding in Go is not as straightforward as in Python or Node.js. I'd have to set up an embedding model locally or use a cloud provider. This is supposed to be a demo, and I didn't plan to use any tokens. That's why I built a Node.js server for embedding text chunks.

The purpose of this demo was to test that my solution works. An setup that introduced complexity unrelated to the goal of notifying users after embedding was complete was removed. That is why I used Genkit's localvector store and not Pinecone or MongoDB. The vector store was not pertinent to the solution.

Aside from these, I made sure I put myself under the same contraints as my groupie:

- Use Go
- Use asynq
