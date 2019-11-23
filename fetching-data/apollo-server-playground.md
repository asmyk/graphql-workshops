# Apollo server playground

Apollo Server sets up the GraphQL Playground so that you can run queries and explore your schema with ease. Go ahead and start your server by running `npm start` and open up the playground in a browser window at `http://localhost:4000/`.

An example client query looks like this

```graphql
query GetSeriesById {
  series(id: 41923) {
    id
    name
    episodes {
      name
      season
    }
  }
}
```

GraphQL queries declaratively describe what data the issuer wishes to fetch from whoever is fulfilling the GraphQL query.

This query fetches probably a lot of data about episodes. What if we need only series name and description?

```graphql
query GetSeriesById {
  series(id: 41923) {
    id
    name
    description
  }
}
```

Ok - that's it. GraphQL server will return only what client wants.

In both examples parameter ID is hardcoded. Queries can be easily parameterized:

```graphql
query GetSeriesById($id: ID!) {
  series(id: $id) {
    id
    name
    episodes {
      name
      season
    }
  }
}
```

