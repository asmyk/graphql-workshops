# Basic query schema definition

A GraphQL Schema Definition is type system that is used to define schema of an API. 

"At the heart of any GraphQL implementation is a description of what types of objects it can return, described in a GraphQL type system and returned in the GraphQL Schema." 

It means that , on server side every query have to be described by SDL\(schema definition language\). The main components of a schema definition are the **types** and their **fields**. Additional information can be provided as custom **directives.**

### Schema definition

Let's start from define simple schema for some page about movies. The most basic item will be Movie item itself: 

```graphql
type Movie {
    name: String
}
```

Common practise in software development is to assign ID's to objects - it allows to make objects unique. Let's make Movie item more specific:

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

Now, every movie can be classified by list of pre-defined genres.

Ok, now let's add another type which describe Series.

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

### Interface

Does `Series` object definition looks similar to `Movie` object? Both objects have some common parts like `name, description and ID`. And, in fact both are describing very similar thing. Let's merge this common functionality.

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

Above example introducing Interface. An _Interface_ is an abstract type that includes a certain set of fields that a type must include to implement the interface. In other words, every object that implements interface have to contain all fields that interface provides. In this case `Series`, and `Movies` have to provide the same common fields, but `Series` provides `Episode`also.

### Not-null fields

 By default, `null` is a permitted value for any type in GraphQL. So what happens when some of the fields from schema returns null? It could happen for many reasons. We can polish schema a  little bit by adding "!" for some fields. System type can guarantee that those fields will be not null at this level

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

Ok. In example above we can assume that some of the fields will be not null when we ask for them from client.

### Entry Point - Query

One last step is create entry point for this objects. In GraphQL it's called Query. It's also object type that is the basis for all Queries. It's called Query by convention and it's a part of spec.

```graphql
type Query {
  episodes(id: String!, season: Int): [Episode]
  series(id: String!): Series
  movie(id: String!): Movie
}
```

In this example, there are three top-level queries operations:

* `series` - accepts a non-null string as a query argument, a Series ID, and returns the Series with given ID
* `movies` - does same for series
* `episodes` - accepts a non-null string and season number, and returns all episodes from given season



