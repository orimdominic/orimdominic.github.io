---
title: "How to Handle MongoDB Transactions on your Local Server in Node.js"
date: 2023-02-16T23:04:19+01:00
author: "Orim Dominic Adah"
description: ""
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["mongodb", "ACID", "transactions", "mongoose", "Node.js"]
---

Until version 4, MongoDB lacked the ability to carry out ACID transactions. ACID transactions enable database operations (creating, reading, updating and deleting data) to be more resilient and dependable. This is necessary, especially in finance-related applications that have to do with transferring money among users.

This guide is for you if you use Node.js and MongoDB. It is expected that you have installed MongoDB already and you have a running MongoDB server on your local computer. You should also have [MongoDB Compass](https://www.mongodb.com/docs/compass/current/install/) installed so that you can view the results of this on a graphical user interface.

## What is ACID?

ACID is an acronymn for
- Atomicity
- Consistency
- Isolation
- Durability

When a database transaction (a group of CRUD operations on a database) is said to be
ACID, it means that the database is always left in a valid state even when errors occur during the transaction. For example, if user A initiates an operation to transfer money from their
account to user B's account, a group of operations (a transaction) would occur
in this order:

1. deduct the amount from user A's balance,
2. add it to user B's balance

If after the decution, an error occurs and a complementary addition could not be effected, the database
would be in an invalid state because funds have been deducted from user A but have not been added to user B. To keep the database in a valid state, the deduction should be reverted. If a revert happens, the transaction is said to be ACID [^1].

## Setting up the MongoDB Server with run-rs

When the default MongoDB application is installed on a local computer and the application is set up to run a MongoDB server, the server is not set up with the replication [^2] feature by default.
[run-rs](https://www.npmjs.com/package/run-rs) is an npm package that will help
start a MongoDB server that is enabled with the replication feature.

In the [MongoDB Transactions API documentation](https://www.mongodb.com/docs/manual/core/transactions/#transactions-api), replica sets are required for ACID transactions in MongoDB. Replica sets are not on MongoDB databases created on a local server by default, but they are present in MongoDB databases created by MongoDB Atlas.

In order to set up a MongoDB server to run with replica sets locally, follow the
steps below

1. Install `run-rs` globally via your terminal using your preferred JavaScript package manager (pnpm, yarn, npm,). This guide uses `pnpm`.

```bash
pnpm add -g run-rs
```

2. Run the package via the terminal with the command in the snippet below. Ensure to run
   it using the MongoDB application that you have installed already using the
   `--mongod` flag. Also ensure that you run it with the `--keep` flag becasue
   every time `run-rs` runs anew, it purges the data that existed in the
   database. The `--keep` flag preserves the data.

   Lastly, make sure that `run-rs` is run on a different port than `27017` with the `--portStart` flag. `27017` is the port that the default MongoDB server runs on when installed. Two servers cannot run on the same port. You can choose another port, for example, `27000`.

```bash
run-rs --mongod --portStart 27000 --keep
```

On successful run, you should see this on the terminal

{{< figure caption="run-rs MongoDB running successfully" align=center src="../images/run-rs-mongodb.png" >}}

## Using the Database Server in a Node.js Application

You have been able to set up a MongoDB server that can handle transactions. To see how it handles transactions and ensures that they are ACID, connect it to a Node.js API application and make requests to the endpoints involved.

### Setting Up

A starting point for the project has been set up on GitHub at [mongodb-transactions](https://github.com/orimdominic/mongodb-transactions). It uses Mongoose as the ODM. In the README, you will find instructions on how to set up the project. You will also find the API documentation in it.

On starting the project, users with funds will be seeded to the database automatically. The project has an API endpoint that executes the transfer of funds from one user to another. The transfer operation will be managed by transactions in MongoDB.

Make sure that the `run-rs` MongoDB server is already running with `run-rs --mongod --portStart 27000 --keep` command in another terminal. You can view the content of the database when you open up your MongoDB Compass application and use `mongodb://localhost:27000/?replicaSet=rs&readPreference=primary&ssl=false` as the connection string.

### Running a Transaction

Use the `GET /users` endpoint to see a list of users. Use the `POST /transfers` endpoint to transfer funds from a user with a lower balance than the transfer amount and take note of the outcome.

You would notice that the transaction fails. This is because an error was thrown in-between deducting funds from the sender's balance and adding funds to the receiver's balance. The whole operation was run in a transaction ([after `session.startTransaction` was executed](https://github.com/orimdominic/mongodb-transactions/blob/ee5c2c7327b44dbd5296254292540650f6f1e763/src/controller/transfer.controller.js#L7)). Since the transaction was not [commited](https://github.com/orimdominic/mongodb-transactions/blob/ee5c2c7327b44dbd5296254292540650f6f1e763/src/controller/transfer.controller.js#L69), no change was applied to the database.

**Note**: In a real application, you should use a double ledger where records of outgoing funds are persisted in a collection and records of incoming funds are persisted in another collection.

[^1]: [What are ACID Properties in Database Management Systems?](https://www.mongodb.com/basics/acid-transactions)
[^2]:
    _replication_ - A feature allowing multiple database servers to share the
    same data, thereby ensuring redundancy and facilitating load balancing. See
    [MongoDB Glossary of Terms](https://www.mongodb.com/docs/manual/reference/glossary/#std-term-replication)
