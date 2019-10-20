# How it works

## Architecture and implementation types

GraphQL is a **specification** that describes the behaviour of a GraphQL server. It is a set of guidelines on how requests and responses should be handled like supported protocols, format of the data that can be accepted by the server, format of the response returned by the server, etc.

### 1.  **GraphQL server with a connected database**

This architecture has a GraphQL Server with an integrated database**.** The client \(desktop/mobile\) communicates with GraphQL server over HTTP. The server processes the request, fetches data from the database and returns it to the client.

![source: www.howtographql.com](../.gitbook/assets/image%20%284%29.png)

### 2. GraphQL layer that integrates existing systems

In that context, GraphQL can be used to _unify_ these existing systems and hide their complexity behind a nice GraphQL API. This way, new client applications can be developed that simply talk to the GraphQL server to fetch the data they need.

![source: www.howtographql.com](../.gitbook/assets/image%20%282%29.png)

### 3. Hybrid approach

It's just a combination of two previous examples. When a query is received by the server, it will resolve it and either retrieve the required data from the connected database or some of the integrated APIs.

![source: www.howtographql.com](../.gitbook/assets/image%20%283%29.png)



