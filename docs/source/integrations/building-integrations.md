---
title: Building Integrations for Apollo Server
description: ""
---

Apollo Server 4 comes with a stable well-defined API for processing HTTP requests and responses.

Writing and maintain integrations should be much easier in the new model, because the only responsibility of these packages will be translating requests and responses between the framework's native format and the types used by ApolloServer's API.

## Integration framework function

Framework integration packages can export functions or classes that take in `ApolloServer` and call its methods appropriately instead of adding their own methods to the object.

Your integration should expect to intake an `ApolloServer` instance, after the `start()` function has been called upon it.

This is often done by passing the server instance to your framework specific middleware function, like we do with the `expressMiddleware` function:

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

