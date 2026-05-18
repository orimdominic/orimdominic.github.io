---
title: "MongoDB Local Development Setup"
date: 2026-05-14T01:02:47+01:00
author: "Orim Dominic Adah"
description: "How I set up MongoDB for local development"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["mongodb", "docker"]
---

Some of the applications I build use MongoDB. [MongoDB community server](https://www.mongodb.com/try/download/community) is the most common MongoDB server for local development but it is not exactly the same as MongoDB Atlas which is used in production. The community server lacks two features:

- Replica sets
- Vector search

MongoDB Atlas has replica sets - one primary database and two secondary databases. The replica sets are important for data replication, redundancy and [transactions](https://www.mongodb.com/docs/manual/core/transactions/). It also has a vector search feature which can be used for Retrieval-Augmented Generation (RAG).

The applications I build require these features but the community server lacks them. Moreover, it's important that the environment used during development is similar to the one in production.

## What I Used Before Now
I used run-rs for replica sets and MongoDB Search and MongoDB Vector Search for vector search.

### run-rs for Replica Sets
[run-rs](https://www.npmjs.com/package/run-rs) was what I previously used to set up a MongoDB server. Setting it up required some hacks but in general, it worked fine. Recently, I mistakenly tampered with some configurations and it cleared the data stored by the package - all the databases I used with run-rs vanished. I needed a durable solution - isn't that what the D in ACID means?

### MongoDB Search and MongoDB Vector Search for Vector Search
Setting up [MongoDB Search and MongoDB Vector Search](https://www.mongodb.com/docs/manual/administration/install-community/?operating-system=linux&linux-distribution=red-hat&linux-package=default&macos-installation-method=None&windows-installation-method=None&search-linux=with-search-linux&search-docker=None#install-mongodb-search-and-mongodb-vector-search) was not a straightforward procedure for me, but I got it to work eventually. I didn't like the manual process I used to always start it up, so I defaulted to using a remote MongoDB Atlas server for RAG, and this was not the best for local development.

## What I Use Now

For replica sets and vector search, I now use Docker images. You can find the setup at [mongo-docker-local-dev](https://github.com/orimdominic/mongo-docker-local-dev). I spin up these Docker images when I need an app to connect to a MongoDB instance locally and I can use the instance with the replica set or the one with the vector search.

### Replica Set
For the [replica set](https://github.com/orimdominic/mongo-docker-local-dev/blob/main/compose-replicas.yml), I set up three MongoDB server instances (mongo_1, mongo_2, mongo_3) and connect them all with a fourth instance(mongo_replicas_init). The [`init_replset`](https://github.com/orimdominic/mongo-docker-local-dev/blob/main/compose-replicas.yml) script contains code that initialises a replica set using the first three MongoDB servers and sets their advertisment URIs. This script runs only once - the first time the container is started.

This setup requires the addition of the URIs of the instances to my `/etc/hosts` file:

```txt
127.0.0.1   mongo_1
127.0.0.1   mongo_2
127.0.0.1   mongo_3
```

This should be done before starting the container.

To start the replica set container, I run `make replicas` to execute the `replicas` target in `Makefile`. I connect with the databases using the URI value `mongodb://mongo_1:27001,mongo_2:27002,mongo_3:27003/?replicaSet=rs`.

To confirm that it works, run `make replicas` in the repository's root folder and then run `node scripts/replset.cjs` in another terminal of the repository's root folder. You would see a `connected to replica set server` message in the terminal.

### Vector Search
I use the `mongodb/mongodb-atlas-local` Docker image to set up a local version of MongoDB Atlas. This version has the vector search feature, the text search and Atlas search features for autocomplete and vector search. It also has a single-node replica set.

I use `make atlas` to start the local atlas server and I connect to it with the URI: `mongodb://root:secret@localhost:27004?directConnection=true`.

## Questions
> Does this setup support [change streams](https://www.mongodb.com/docs/manual/changestreams/)?

  The replica set and the atlas setups both support change streams.

> Why didn't you create a three-node replica set with the local atlas server so you have the replica sets and the vector search features in one container?

  I thought you wouldn't ask. It doesn't work. The local atlas server is a single-node replica set server and that cannot be modified (yet).

Cheers!
