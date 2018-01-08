---
alias: xil8ahdayo
description: Learn the fundamentals of using Graphcool.
---

# Graphcool Basics

In this tutorial, you'll learn how to get started with Graphcool as a "GraphQL database" service.

Here's an overview of what we are going to cover:

- Creating a new Graphcool service
- Changing the data model (schema) of a Graphcool service
- Understanding the relationship between your data model and the generated GraphQL API
- Send queries and mutations to the service's API in a GraphQL Playground

> Note that this tutorial really only covers the basics of using Graphcool. To learn how to use Graphcool with a specific framework or programming language (like React, TypeScript or Node.js), please refer to the other quickstart tutorials.

## Installing the Graphcool CLI

Graphcool services are managed with the [Graphcool CLI](!alias-ieshoo5ohm). You can install it using `npm` (or `yarn`).

<Instruction>

Open your terminal and run the following command to install the Graphcool CLI:

```sh
npm install -g graphcool
# or
# yarn global add graphcool
```

</Instruction>

## Bootstraping a Graphcool service

A Graphcool service requires at least two files so it can be deployed:

- [`graphcool.yml`](!alias-foatho8aip): The root configuration file for your service. It contains information about your service, like the name (which is used to generate the service's HTTP endpoint), a secret to secure the access to the endpoint and about where it should be deployed.
- `datamodel.graphql` (can also be called differently, e.g. `types.graphql`): This file contains the definition of your [data model](!alias-eiroozae8u), written in [GraphQL SDL](https://blog.graph.cool/graphql-sdl-schema-definition-language-6755bcb9ce51).

To get started with your own Graphcool service, you can either create these files yourself and configure them accordingly - or you can use the `graphcool init` command from the Graphcool CLI to bootstrap a minimal setup for your GraphQL server.

<Instruction>

Open a terminal and navigate to a folder of your choice. Then bootstrap your Graphcool service with the following command:

```sh
graphcool init hello-world
```

</Instruction>

This will create a new directory called `hello-world` as well as the two files mentioned above to provide a minimal setup for the service.

> **Note**: The `hello-world` directory actually contains a third file as well: `.graphqlconfig.yml`. This file follows the industry standard for configuring and structuring GraphQL projects (based on [`graphql-config`](https://github.com/graphcool/graphql-config)). If present, it is used by GraphQL tooling (such as the GraphQL Playground, the [`graphql-cli`](https://github.com/graphql-cli/graphql-cli/), text editors, build tools and others) to improve your local developer workflows.

Let's take a look at the contents of the generated files:

**`graphcool.yml`**

```yml
service: hello-world
stage: dev

datamodel: datamodel.graphql

# to enable auth, provide
# secret: my-secret
disableAuth: true
```

Here's an overview of the properties in the generated `graphcool.yml`:

- `service`: Defines the service name which will be part of the service's HTTP endpoint
- `stage`: A service can be deployed to multiple stages (e.g. a _development_ and a _production_ environment)
- `datamodel`: The path to the file which contains your data model
- `disableAuth`: If set to true, everyone who knows the endpoint of your Graphcool service has full read and write access. If set to `false`, you need to specify a `secret` which is used to generate JWT authentication tokens. These tokens need to be attached to the `Authorization` header of the requests sent to your service.

> **Note**: We'll keep `disableAuth` set to `true` for this tutorial. In production applications, you'll always want to require authentication for your service! You can read more about this topic [here](!alias-pua7soog4v).

**`datamodel.graphql`**

```graphql
type User {
  id: ID! @unique
  name: String!
}
```

The datamodel contains type definitions for the entities in your application domain. In this case, you're starting out with a very simple `User` type with an `id` and a `name`.

The `@unique` directive here expresses that no two users in the database can have the same `id`, Graphcool service will ensure this requirement is met at all times.

## Deploying your Graphcool service

`graphcool.yml` and `datamodel.graphql` are your abstract _service definition_. To actually create an instance of this service that can be invoked via HTTP, you need to _deploy_ it.

<Instruction>

Inside the `hello-world` directory in your terminal, run the following command:

```sh
graphcool deploy
```

</Instruction>

Since `graphcool.yml` doesn't yet contain the information about _where_ (meaning to which `cluster`) your service should be deployed, the CLI triggers a prompt for you to provide this information. At this point, you can choose to either deploy it locally with [Docker](https://www.docker.com) (which of course requires you to have Docker installed on your machine) or to a public Graphcool cluster. You'll use a public cluster for the purpose of this tutorial.

<Instruction>

Select the `shared-public-demo` option in the **Shared Clusters** section.

</Instruction>

Your Graphcool service is now deployed and ready to accept your queries and mutations 🎉

## Exploring your service in a GraphQL Playground

So your service is deployed - but how do you know how to interact with it? What does its API actually look like?

In general, the generated API allows to perform CRUD operations on the types in your data model. It also exposes GraphQL subscriptions which allow clients to _subscribe_ to certain _events_ and receive updates in realtime.

Because your datamodel contains the `User` type, the Graphcool API now allows for its clients to create, read, update and delete instances, also called _nodes_, of that type. In particular, the following GraphQL operations are now generated based on the `User` type:

- `user`: Query to retrieve a single `User` node by its `id` (or another `@unique` field).
- `users`: Query to retrieve a list of `User` nodes.
- `createUser`: Mutation to create a new `User` node.
- `updateUser`: Mutation to update an existing `User` node.
- `deleteUser`: Mutation to delete an existing `User` node.

> **Note**: This list of generated operations is not complete. The Graphcool API exposes a couple of more convenience operations that, for example, allow to batch update/delete many nodes. However, all operations either create, read, update or delete nodes of the types defined in the data model.

To actually use these operations, you need a way to [send requests to your service's API](ohm2ouceuj). Since that API is exposed via HTTP, you could use tools like [`curl`](https://en.wikipedia.org/wiki/CURL) or [Postman](https://www.getpostman.com/) to interact with it. However, GraphQL actually comes with much nicer tooling for that purpose: [GraphQL Playground](https://github.com/graphcool/graphql-playground), an interactive GraphQL IDE.

<Instruction>

To open a GraphQL Playground, you can use the Graphcool CLI again. Simply run the following command inside the `hello-world` directory:

```sh
graphcool playground
```

</Instruction>

This will open a Playground looking as follows:

![](https://imgur.com/QWfgonS.png)

> **Note**: The Playground can be installed on your machine as a standalone application. If you don't have the Playground installed, the command automatically opens a Playground in your default browser.

One really cool property of GraphQL APIs is that they're effectively _self-documenting_. The [GraphQL schema](https://blog.graph.cool/graphql-server-basics-the-schema-ac5e2950214e) defines all the operations of an API, including input arguments and return types. This allows for tooling like the GraphQL Playground to auto-generate API documentation.

<Instruction>

To see the documentation for your service's API, click the green **SCHEMA** button on the right edge of the Playground window.

</Instruction>

This brings up the Playground's documentation pane. The left-most column is a list of all the operations the API accepts. You can then drill down to learn the details about the input arguments or return types that are involved with each operation.

![](https://imgur.com/PdmvNy9.png)

## Sending queries and mutations

All right! With everything you learned so far, you're ready to fire off some queries and mutations against your API. Let's start with the `users` query to retrieve all the `User` nodes currently stored in the database.

<Instruction>

Enter the following query into the left Playground pane and click the **Play**-button (or use the hotkey **CMD+Enter**):

```graphql
query {
  users {
    name
  }
}
```

</Instruction>

At this point, the server only returns an empty list. This is no surprise as we haven't actually created any `User` nodes so far. So, let's change that and use the `createUser` mutation to store a first `User` node in the database.

<Instruction>

Open a new tab in the Playground, enter the following mutation into the left Playground pane and send it:

```graphql
mutation {
  createUser(data: {
    name: "Sarah"
  }) {
    id
  }
}
```

</Instruction>

This time, the response from the server actually contains some data (note that the `id` will of course vary as the server generates a globally unique ID for every new node upon creation):

```json
{
  "data": {
    "createUser": {
      "id": "cjc69nckk31jx01505vgwmgch"
    }
  }
}
```

<Instruction>

You can now go back to the previous tab with the `users` query and send that one again.

</Instruction>

This time, the `User` node that was just created is returned in the server response:

![](https://imgur.com/AE5Z5Vu.png)

## Extending the API and adjusting the data model