# Schema example for paginated items

There are a couple of options how to approach into GraphQL pagination. Look at these three proposals how to approach:

* We could do something like `friends(first:2 offset:2)` to ask for the next two in the list.
* We could do something like `friends(first:2 after:$friendId)`, to ask for the next two after the last friend we fetched.
* We could do something like `friends(first:2 after:$friendCursor)`, where we get a cursor from the last item and use that to paginate.

## Relay Cursor Connections

But, in this tutorial we will focus on Relay Cursor Connections spec. Look at example below

```graphql
{
  user {
    id
    name
    friends(first: 10, after: "opaqueCursor") {
      edges {
        cursor
        node {
          id
          name
        }
      }
      pageInfo {
        hasNextPage
      }
    }
  }
}
```

In this case, `friends` is a connection. That query demonstrates the four features described above:

* Slicing is done with the `first` argument to `friends`. This asks for the connection to return 10 friends.
* Pagination is done with the `after` argument to `friends`. We passed in a cursor, so we asked for the server to return friends after that cursor.
* For each edge in the connection, we asked for a cursor. This cursor is an opaque string, and is precisely what we would pass to the `after` arg to paginate starting after this edge.
* We asked for `hasNextPage`; that will tell us if there are more edges available, or if we’ve reached the end of this connection.

## GraphQL Server Implementation

You may wondering how many changes require this kind of pagination. Let's start from refactor schema to something like this:

```graphql
  type Query {
    movies(
      pageSize: Int
      after: String
    ): MovieConnection!
  }

  type MovieConnection {
    edges: [MovieEdge!]!
    totalCount: Int
    pageInfo: PageInfo
  }

  type MovieEdge {
    node: Movie!
    cursor: String
  }

  type Movie {
    id: ID!
    name: String
    description: String
  }

  type PageInfo {
    cursor: String!
    hasNextPage: Boolean!
  }
```

It is how server schema definition looks like for paginated list. Object are wrapped in another objects but this kind of relation gives us a lot of flexibility.

