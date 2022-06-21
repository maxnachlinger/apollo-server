---
title: Building Integrations for Apollo Server
description: ""
---

One of the driving forces behind Apollo Server 4 is the creation of a stable, well-defined API for processing HTTP requests and responses. Apollo Server 4's API enables external collaborators (like you) to build integrations with Apollo Server in their web framework of choice.

The primary responsibility of an Apollo Server integration is to translate requests and responses between a web framework's native format to the format that `ApolloServer` uses.
## Patterns for integrations

<!-- TODO: fix link to point to main once merged  -->
> See the [`expressMiddleware` function](https://github.com/apollographql/apollo-server/blob/36482f5eb56a0421c1eb47e3ebf0e60e033573ab/packages/server/src/express/index.ts) for an example of integrating [Express](https://github.com/expressjs/express) with Apollo Server.

Your integration should expect to intake an `ApolloServer` instance _after_ that instance has called the `server.start()` method:

```ts
// Create a new instance of ApolloServer
const server = new ApolloServer({
  typeDefs,
  resolvers,
});

// The async start method instructs Apollo Server to
// prepare to handle incoming operations.
await server.start();
```

You can then pass the "started" `ApolloServer` instance into your framework-specific middleware. For example, the `expressMiddleware` function accepts an `ApolloServer` instance, like so:

```ts
// 1. Create our ApolloServer instance and start it
const server = new ApolloServer({
  typeDefs,
  resolvers,
});
await server.start();

// 2. Create our Express server
const app = express();
const httpServer = http.createServer(app);

// 3. Set up our middleware for our Express server.
// The expressMiddleware function accepts an ApolloServer instance,
// attaching Apollo Server to our Express server.
app.use(expressMiddleware(server));
```

After setting up the framework-specific middleware, you can start your chosen framework's server:

```ts
// 1. Create our ApolloServer instance and start it
const server = new ApolloServer({
  typeDefs,
  resolvers,
});
await server.start();

// 2. Create our framework specific server
const app = express();
const httpServer = http.createServer(app);

// 3. Set up our middleware for our framework specific server.
app.use(cors(), bodyParser.json(), expressMiddleware(server));

// 4. Start the framework specific server
await new Promise<void>((resolve) => httpServer.listen({ port: 4000 }, resolve));
console.log(`🚀 Server ready at http://localhost:4000`);
```

Note, that your integration is responsible for setting up [body-parser](https://www.npmjs.com/package/body-parser) and [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) configuration.


