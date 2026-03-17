---
title: "Set Custom Types for docs items in mongoose-paginate-v2"
date: 2023-09-19T19:11:35+01:00
author: "Orim Dominic Adah"
description:
  "With mongoose-paginate-v2 you can now set the type of the items in 'docs'
  array"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["mongoose-paginate", "mongoose", "pagination"]
cover:
  image: "../posts/images/set-custom-types-for-docs-items-in-mongoose-paginate-v2.png"
  alt: "Set Custom Types for docs items in mongoose-paginate-v2 wit TypeScript"
  caption: ""
  relative: false
---

## What is mongoose-paginate-v2?

[mongoose-paginate-v2](https://www.npmjs.com/package/mongoose-paginate-v2) is a
pagination library for [mongoose](https://mongoosejs.com/). With
mongoose-paginate-v2, you can fetch a list of documents from a MongoDB
collection and get a paginated response.

Paginated responses are useful for cases where UI clients needs to query and
display only a section of data at a time.

{{< figure caption="UI Table Pagination - ui.shadcn.com" align=center src="../images/paginated-table-items.png" >}}

The mongoose-paginate-v2 documentation provides a code snippet for
[how to use the library with TypeScript](https://github.com/aravindnc/mongoose-paginate-v2#typescript).
[^1]

## Setting the type of `docs` items

Prior to version 1.7.4 of mongoose-paginate-v2, it was not possible to set the
type of the items in `docs`. The type of the items in `docs` was set to the type
passed in when the mongoose model was created.

```ts
import { model, PaginateModel, Schema, Types } from "mongoose";
import paginate from 'mongoose-paginate-v2';

// Create the model interface
interface Person {
  _id: Types.ObjectId
  name: string
  birthYear: number
  favouriteFood: string
}

// Create the schema
const personSchema = new mongoose.Schema({
  name: String;
  birthYear: Number;
  favouriteFood: String;
 });

// attach the mongoose-paginate-v2 plugin
personSchema.plugin(paginate);

// create the paginated model
// PaginateModel<Person> sets the type of docs items to Person
const PersonModel = model<Person, PaginateModel<Person>>('Person', personSchema);

// Create documents
await PersonModel.create([
  {
    name: "John Doe",
    birthYear: 1970
    favouriteFood: "Bacon"
  },
  {
    name: "Jane Doe"
    birthYear: 1970
    favouriteFood: "French fries"
  }
])

// Get a paginated list of documents
const { docs, totalDocs, page, prevPage, nextPage, totalPages } =
  await Business.paginate(
    {},
    {
      offset: 1,
      limit: 1,
    }
  );
```

In the example above, the items in the `docs` array are typed to `Person`,
and `docs` will have a type of `Person[]`. Everything is fine until you use the
[select](<https://mongoosejs.com/docs/api/query.html#Query.prototype.select()>),
the
[populate](<https://mongoosejs.com/docs/api/query.html#Query.prototype.populate()>)
or the other mongoose options that change the output like
[projection](<https://mongoosejs.com/docs/api/aggregate.html#Aggregate.prototype.project()>)
and `fields`.

```ts
const { docs, totalDocs, page, prevPage, nextPage, totalPages } =
  await Business.paginate(
    {},
    {
      select: "-favouriteFood",
      offset: 1,
      limit: 1,
    }
  );
```

In the code snippet above, we exclude the `favouriteFood` field from the items
in the `docs` fields returned. In other words, the type of `docs` should be an
array of

```ts
{
  _id: Types.ObjectId;
  name: string;
  birthYear: number;
}
```

but it is not so. `docs` is typed to `Person[]` with all the fields included,
which can be deceiving.

## Setting Custom Types for `docs` Items

mongoose-paginate-v2 version 1.7.4 now provides an option that can be used to
[dynamically set the type of `docs` items](https://github.com/aravindnc/mongoose-paginate-v2/pull/201).

Setting the type of the items returned in the `docs` array can be done in either
of two ways:

1. Passing the desired type as the first argument to the `paginate` generic
2. Passing the desired type as the second argument to the `paginate` generic

### Passing the desired type as the first argument to the `paginate` generic

```ts
type PersonWithoutFavouriteFood = {
  _id: Types.ObjectId;
  name: string;
  birthYear: number;
};

const { docs, totalDocs, page, prevPage, nextPage, totalPages } =
  await Business.paginate<PersonWithoutFavouriteFood>(
    {},
    {
      select: "-favouriteFood",
      offset: 1,
      limit: 1,
    }
  );
```

You can use this method if you do not want to modify the type of the options
passed in as the second argument to the `paginate` function.

### Passing the desired type as the second argument to the `paginate` generic

```ts
import { PaginateOptions } from "mongoose";

type PersonWithoutFavouriteFood = {
  _id: Types.ObjectId;
  name: string;
  birthYear: number;
};

const { docs, totalDocs, page, prevPage, nextPage, totalPages } =
  await Business.paginate<PaginateOptions, PersonWithoutFavouriteFood>(
    {},
    {
      select: "-favouriteFood",
      offset: 1,
      limit: 1,
    }
  );
```

You can use this method if you want to modify the type of the options passed in
as the second argument to the `paginate` function. The type is `PaginateOptions`
by default.

[^1]: [Mongoose no longer supports extending Document from v7](https://mongoosejs.com/docs/migrating_to_7.html#removed-leandocument-and-support-for-extends-document)

 Shameless plug - I authored this feature.
