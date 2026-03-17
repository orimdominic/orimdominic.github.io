---
title: "Write Integration Tests with NestJS, TypeORM and MongoDB"
date: 2022-04-07T06:11:14+01:00
author: "Orim Dominic Adah"
description: "Writing integration tests in NestJS using TypeORM and MongoDB"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["nestjs", "integration testing", "typeorm", "mongodb"]
cover:
  image: "../posts/images/nestjs-integration-test-mongo-typeorm.png"
  alt: "NestJS Integration Testing with TypeORM and MongoDB"
  caption: ""
  relative: false
---

NestJS projects that use TypeORM with MongoDB are rare. The recommended ODM[^1]
to use with MongoDB is Mongoose, but the choice is not always within our
control. This article is a guide for writing integration tests in a NestJS
project that uses TypeORM with MongoDB.

Integration tests verify that all units required to accomplish a particular goal
work together as expected. For integration tests in a backend application,
database calls are not mocked. API calls to external services such as Slack or
Stripe may be mocked.

## The NestJS Sample Project

The NestJS sample project for this article can be found at
[orimdominic/nest-mongo-typeorm-e2e](https://github.com/orimdominic/nest-mongo-typeorm-e2e).
The project database has a collection of users and items. Each user can have a
list of items saved to their account.

There are endpoints to

1. create a user `POST /users`,
2. create a user's item `POST /items`,
3. get items `GET /items` and,
4. get an item `GET /items/:id`.

All these endpoints and the functionaity surrounding them have been created. All
we want to do is write integration tests for them.

## Setting Up The Project

To set up a basic NestJS project,

1. install the NestJS CLI globally using your package manager
   `npm i -g @nestjs/cli` or `yarn global add @nestjs/cli`
2. create a new project with `nest new project-name`

Other libraries to install include

- [`mongodb`](https://www.npmjs.com/package/mongodb) < v4. [^2]
- [`typeorm`](https://www.npmjs.com/package/typeorm) and
  [`@nestjs/typeorm`](https://www.npmjs.com/package/@nestjs/typeorm)
- [`mongodb-memory-server`](https://www.npmjs.com/package/mongodb-memory-server)

```bash
# within the project directory's terminal
npm install mongodb@3.7.0 typeorm @nestjs/typeorm mongodb-memory-server
```

## Setting Up The Tests

We want to use two databases; `mongodb-memory-server` for integration tests and
`mongodb` for running the application. In `app.module.ts`, we set up the
TypeORM-mongodb connection to the database for running the application.

```ts
/**
 * @file app.module.ts
 */
//...other imports

@Module({
  imports: [
    TypeOrmModule.forRoot({
      url: 'mongodb://127.0.0.1:27017/mongo-typeorm-e2e',
      type: 'mongodb',
      entities: [join(__dirname + '/**/*.entity{.ts,.js}')],
    }),
    // ...
  ],
//...
})
```

## Testing UsersController

We create a spec file with the name `users.e2e-spec.ts`. The file name must end
in `e2e-spec.ts` because that is the filter that NestJS uses to find
integration/end-to-end test files. The `users.e2e-spec.ts` file will contain
tests for API calls to routes handled by UsersController.

We have only one endpoint here - `POST /users` which is used to create users. In
setting up the test for the endpoint, before any test is run, we

1. create an instance of `mongo-memory-server`
2. create a fixture module to boostrap the application and setup its endpoints
   for receiving requests
3. override the `TypeOrmModuleOptions` token so that we can replace the database
   connection with that of our mongo-memory-server instance [^3]
4. create the application from the fixture module
5. intialise the the application
6. get the tokens that we need from the application for the module that is under
   test

```ts
/**
 * @file users.e2e-spec.ts
 */
describe("UsersController (e2e)", () => {
  let app: INestApplication,
    mongod: MongoMemoryServer,
    usersRepo: MongoRepository<User>;

  beforeAll(async () => {
    mongod = await MongoMemoryServer.create(); // 1
    const moduleFixture: TestingModule = await Test.createTestingModule({
      // 2
      imports: [AppModule],
    })
      .overrideProvider("TypeOrmModuleOptions") // 3
      .useValue({
        url: mongod.getUri(),
        type: "mongodb",
        entities: [join(__dirname + "/**/*.entity{.ts,.js}")],
      })
      .compile();

    app = moduleFixture.createNestApplication(); // 4
    await app.init(); // 5

    usersRepo = moduleFixture.get(getRepositoryToken(User)); // 6
  });
});
```

To test the `POST /users` route, we include the following test suite in the
file, on the same level as the `beforeAll` block.

```ts
describe("POST /users", async () => {
  afterEach(async () => {
    await usersRepo.deleteMany({}); // 1
  });

  it("creates a new user", () => {
    return request(app.getHttpServer())
      .post("/users")
      .send({
        name: "Demo User",
        email: "demo.user@mail.com",
      })
      .expect(201)
      .expect((res) => {
        expect(res.body).toMatchObject({
          name: "Demo User",
          email: "demo.user@mail.com",
          _id: expect.any(String),
        });
      });
  });
});
```

At `1`, we want to clear all data in the users collection after each test so
that each test can start with an empty collection of users. The
`creates a new user` test block contains the test to verify that a new user can
be successfully created by the `POST /users` endpoint if the right payload is
passed to it.

At this point, running `yarn test:e2e users` will work, but there will be
issues. The test suite will not exit when done because the Nest application was
not shut down. To shut it down, we introduce the `afterAll` block on the same
level as the `beforeAll` block, with the command to shut down the application
for this test suite. `app.close()` explicitly shuts down the application. We
also stop the mongod running instance.

```ts
afterAll(async () => {
  await mongod.stop();
  await app.close();
});
```

Running `yarn test:e2e users` should work as expected now.

## Testing ItemsController

The same procedure used in testing `UsersController` is used in testing
`ItemsController`.

An e2e test file is created - `items.e2e-spec.ts`, a database instance of the
mongo-memory-server is created and is used to override the application's
database connection. An application for the test is bootstrapped and the
necessary tokens are retrieved. The application is closed after all tests have
run.

**Note**: You may see the error
`[ExceptionHandler] Cannot execute operation on "default" connection because connection is not yet established.`
on your console. This occurs before the items controller test is is run. I have
debugged this but I couldn't find the source. If you find a fix or its source,
please reach out to me on [Twitter](https://twitter.com/orimdominic_) or
[LinkedIn](https://linkedin.com/in/orimdominicadah).

[^1]:
    ODM: Object Document Mapper. A library that maps data in a programming
    language to documents in a database.

[^2]:
    TypeORM does not support mongdb drivers > v3 at the time of writing. Take a
    look at this issue:
    [Add support for mongodb driver v4](https://github.com/typeorm/typeorm/issues/7907)

[^3]:
    Many thanks to [Jay McDoniel](https://github.com/jmcdo29) for his input on
    overriding the database provider in the tests instead of in a conditional in
    [`app.module.ts`](https://github.com/orimdominic/nest-mongo-typeorm-e2e/blob/bc1c6015eb3f46d9eb9bc9f16b5eb656f5b683bb/src/app.module.ts)

[Software code testing Cartoon Illustrations

- Image Work illustrations by Storyset](https://storyset.com/work)
