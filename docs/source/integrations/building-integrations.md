---
title: Building Integrations for Apollo Server
description: ""
---

One of the driving forces behind Apollo Server 4 was the creation of a stable, well-defined API for processing HTTP requests and responses. This new API makes opens up the possibility for external collaborators to build integrations for their framework of choice for Apollo Server 4.
## Building a integration package

The primary responsibility of an integration for Apollo Server 4 is translating requests and responses between the framework's native format and the types used by `ApolloServer`'s API.

Your integration should expect to intake an `ApolloServer` instance, after the `server.start()` function has been called upon it. This is often done by passing the server instance to your framework specific middleware function, like we do with the `expressMiddleware` function:

<MultiCodeBlock>

```ts
import { ApolloServer, expressMiddleware } from '@apollo/server';
import express from 'express';
import http from 'http';
import cors from 'cors';
import bodyParser from 'body-parser';

const server = new ApolloServer({
  typeDefs,
  resolvers,
});

await server.start();
const app = express();
const httpServer = http.createServer(app);

// Passing in an `ApolloServer` instance to the `expressMiddleware` function,
// attaching Apollo Server to our Express server.
app.use(cors(), bodyParser.json(), expressMiddleware(server));
await new Promise<void>((resolve) => httpServer.listen({ port: 4000 }, resolve));

console.log(`🚀 Server ready at http://localhost:4000`);
```

</MultiCodeBlock>

Your integration is responsible for setting up any [body-parser](https://www.npmjs.com/package/body-parser) or [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) configuration itself.

<!-- TODO: fix link's branch to point to main once merged  -->
> Check out the [`expressMiddleware` function](https://github.com/apollographql/apollo-server/blob/36482f5eb56a0421c1eb47e3ebf0e60e033573ab/packages/server/src/express/index.ts) for an example of how to build an integration function.

