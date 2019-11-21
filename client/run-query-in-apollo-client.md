# Run query in Apollo client

To connect Apollo Client into react App, we have to init ApolloClient object with some configuration data, and connect that client with UI. This allows us to easily bind GraphQL operations to our UI.

{% code title="index.js" %}
```jsx
import React from 'react';
import ReactDOM from 'react-dom';

import { ApolloClient } from 'apollo-client';
import { InMemoryCache } from 'apollo-cache-inmemory';
import { HttpLink } from 'apollo-link-http';
import { ApolloProvider } from '@apollo/react-hooks';
import gql from 'graphql-tag';

import Pages from './pages';
import { resolvers, typeDefs } from './resolvers';
import injectStyles from './styles';

// Set up our apollo-client to point at the server we created
// this can be local or a remote endpoint
const cache = new InMemoryCache();
const client = new ApolloClient({
  cache,
  link: new HttpLink({
    uri: 'http://localhost:4000/graphql',
  }),
  resolvers,
  typeDefs,
});

injectStyles();
ReactDOM.render(
  <ApolloProvider client={client}>
    <Pages />
  </ApolloProvider>,
  document.getElementById('root'),
);

```
{% endcode %}

And that's it. Now, lets look how simple query can be executed.

