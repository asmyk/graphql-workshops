# Basic query schema definition

The GraphQL Schema Definition is type system that is used to define schema of an API.

"At the heart of any GraphQL implementation is a description of what types of objects it can return, described in a GraphQL type system and returned in the GraphQL Schema."

It means that on server side every query has to be described by SDL\(schema definition language\). The main components of a schema definition are the **types** and their **fields**. Additional information can be provided as custom **directives.**

## Schema definition

Let's start from defining a simple schema for some page about movies. The most basic item will be the Movie item itself:

```graphql
type Movie {
    name: String
}
```

Common practice in software development is to assign ID's to objects - it allows to make objects unique. Let's make the Movie item more specific:

```graphql
type Movie {
    id: ID
    name: String
    description: String
}
```

Since we're talking about movies, it would be useful to add some field which describes movie genere.

```graphql
enum Genere { ACTION, HORROR, COMEDY, THRILLER }

type Movie {
    id: String
    name: String
    description: String
    genere: [Genere]
}
```

Now, every movie can be classified by a list of pre-defined genres.

Ok, now let's add another type which describe the Series.

```graphql
type Episode {
    name: String
    season: Int
}

type Series {
    id: String
    name: String
    description: String
    episodes: [Episode]
}
```

## Interface

Does `Series` object definition look similar to `Movie` object? Both objects have some common parts like `name, description and ID`. And, in fact, both are describing a very similar thing. Let's merge this common functionality.

```graphql
enum Genere { ACTION, HORROR, COMEDY, THRILLER }

interface Video {
    id: String
    name: String
    description: String
}

type Movie implements Video {
    id: String
    name: String
    description: String
    genere: [Genere]
} 

type Episode {
    name: String
    season: Int
}

type Series implements Video {
    id: String
    name: String
    description: String
    episodes: [Episode]
}
```

The above example introducing an Interface. An _Interface_ is an abstract type that includes a certain set of fields that a type must include to implement the interface. In other words, every object that implements the interface has to contain all fields that the interface provides. In this case `Series`, and `Movies` have to provide the same common fields, but `Series` provides also `Episode`.

## Not-null fields

By default, `null` is a permitted value for any type in GraphQL. So what happens when some of the fields from our schema return null? It could happen for many reasons. We can polish the schema a little bit by adding "!" for some fields. The system type can guarantee that those fields will not be null at this level.

```graphql
enum Genere { ACTION, HORROR, COMEDY, THRILLER }

interface Video {
    id: String!
    name: String
    description: String
}

type Movie implements Video {
    id: String!
    name: String
    description: String
    genere: [Genere]
} 

type Episode {
    id: String!
    season: Int
    name: String
}

type Series implements Video {
    id: String!
    name: String
    description: String
    episodes: [Episode]
}
```

Ok. In the example above we can assume that some of the fields will not be null when we ask for them from a client.

## Entry Point - Query

The one last step is to create an entry point for this objects. In GraphQL it's called Query. It's also an object type that is the basis for all Queries. It's called Query by convention and it's a part of the spec.

```graphql
type Query {
  episodes(id: String!, season: Int): [Episode]
  series(id: String!): Series
  movie(id: String!): Movie
}
```

In this example, there are three top-level queries operations:

* `series` - accepts a non-null string as a query argument, the Series ID, and returns the Series with given ID
* `movies` - does same for series
* `episodes` - accepts a non-null string and season number, and returns all episodes from given season

