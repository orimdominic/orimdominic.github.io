---
title: "Push Notifications in Go: Notify Users After Long RAG Processing"
date: 2026-05-22T18:12:51+01:00
author: "Orim Dominic Adah"
description: "A demo project in which I use push notifications in Go to notify a client after their document embedding process is completed"
draft: false
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
processing or failed. I created another endpoint that the
frontend polls for the status of the task.

I feel that polling is not necessary and this process is
not optimal. Is there a better way to do this?
```

I sketched a solution and after considering the different client-server communication protocols available, I decided to use push notifications because:

- It is free
- It is not as resource-intensive as server-sent events and web sockets

I responded with my solution, but I felt that it wasn't enough. *Ideas are useless, execution is everything.* (Scott Adams)

I decided to implement my solution to see if what I designed actually works and that's how [sally](https://github.com/orimdominic/sally) was born. sally is a full-stack RAG application (without an LLM) built with:

- Go
- Node.js
- Genkit
- Redis

## How does sally work?

![Sally Architecture - How does sally Work?](../images/sally-arch.png)

The web client (frontend) of the application is a PWA. When loaded, it makes a request to the server for a VAPID public key and uses this key to generate its push subscription identity. The push subscription identity is like the phone number of the client tied server that issued the key used to generate it. The server uses the identity to push notifications to the client.

When the user uploads a `.pdf` document for RAG, the document is sent with the client's push subscription identity to the server via HTTP. The document is received by the server, validated and saved. After saving the file, two operations are triggered:

- A background process is triggered to index the document
- A response is sent to the user to end the request/response cycle immediately

The user gets a response from the request to let them know that their document is being processed and they will be notified when the processing is complete. After they receive a success notification, they can make their search.

{{< video  src="../assets/sally-demo.mp4"  width="600" style="align='center'" >}}

### How is indexing done?

During indexing, text is extracted from the document and split into chunks by the Go server. These chunks are sent to a Node.js server via HTTP for embedding and the embeddings returned are stored using Genkit's localvector store.

### How is the user notified?

After indexing, a web push notification is sent to the user from the background process to notify them of the status of the processed document.

## What constraints did I work under?

I worked under the contraints my groupie worked under - use Go and [asynq](https://github.com/hibiken/asynq). Every other part was decided by me based on its relevance to the solution.

The purpose of this demo was to test that my solution works. Any setup that introduced complexity unrelated to the goal of notifying users was removed. That is why I used Genkit's localvector store and not Pinecone or MongoDB. The vector store was not pertinent to the solution.

Embedding in Go is not as straightforward as in Python or Node.js. I'd have to set up an embedding model locally or use a cloud provider. This was supposed to be a demo, and I didn't plan to use any tokens. That's why I built a Node.js server for embedding text chunks.

I didn't save the subscription identity of the frontend on the server. In a production-grade application, that's what I'm supposed to do. This wasn't relevant to proving that my solution worked, so I used the simplest method possible.

## Conclusion

While push notifications work, for durable notification messages, you should use emails.

You can give sally a run. The instructions for setting her up are in the README. As long as you have Docker, Go and Node.js, you're good to go.

Cheers!
