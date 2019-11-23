# Connect resolver with data source

Now it's time to connect Query definition with resolvers and data-sources.

## **What is a resolver?**

**Resolver** is a function which turns a GraphQL operation \(a query, mutation, or subscription\) into data. They either return the same type of data we specify in our schema or a promise for that data.

The definition of a resolver function looks like this

```javascript
fieldName: (parent, args, context, info) => data;
```

* **parent**: An object that contains the result returned from the resolver on the parent type
* **args**: An object that contains the arguments passed to the field
* **context**: An object shared by all resolvers in a GraphQL operation. We use the context to contain per-request state such as authentication information and access our data sources.
* **info**: Information about the execution state of the operation which should only be used in advanced cases

## How to apply resolver?

First, let's create a file which contains an Object that maps to Query fields:

{% code title="src/resolvers.js" %}
```javascript
module.exports = {
  Query: {
    episodes: (_, { id, season }, { dataSources }) =>
      dataSources.series.getEpisodesForGivenSeries({id, season}),
    series: (_, { id }, { dataSources }) =>
      dataSources.series.getSeriesById({ id }),
    movie: (_, { id }) => dataSources.movies.getMovieById({ id })
  }
};
```
{% endcode %}

Those three functions corresponds to Query fields. They will be invoked when client asks for some of those fields.

Resolvers should be added to Apollo Server resolvers map.

{% code title="src/index.js" %}
```text
...
const resolvers = require('./resolvers');

...

const server = new ApolloServer({
  typeDefs,
  resolvers
});
```
{% endcode %}

Now, they are visible for Apollo server, and each function will be executed when some client askes for data.

## Data sources

They are helpful when we want to connect others services into GraphQL layer. In following section we will apply some REST services into GraphQL layer, and make them visible to resolvers.

To get started install the following package:

```text
npm install apollo-datasource-rest --save
```

Then let's create a file which represents a single data source:

{% code title="src/datasources/series.api.js" %}
```javascript
const { RESTDataSource } = require('apollo-datasource-rest');

class SeriesAPI extends RESTDataSource {
  constructor() {
    super();
    this.baseURL = 'https://api.mymoviesapp.com/v2/';
  }
  async getSeriesById({ id }) {
    const response = await this.get('series', { id: id });
    return this.seriesReducer(response[0]);
  }
  // seriesItem is an object probivied by REST API response
  seriesReducer(seriesItem) {
    return {
      id: seriesItem.id || 0,
      name: seriesItem.seriesName,
      description: seriesItem.description
      episodes: episodesReducer(seriesItem.episodes_list)
  };
}

module.exports = SeriesAPI;
```
{% endcode %}

There are couple important things to notice about this example. A `RESTDataSource` class provides data fetching logic, as well as caching and deduplication. Also, this class sets up an in-memory cache that caches responses from our REST resources. It's called **partial query caching**. What's great about this cache is that you can reuse existing caching logic that your REST API exposes.

Now, let's make data-sources visible for resolvers by adding them into Apollo server:

{% code title="src/index.js" %}
```text
...
const resolvers = require('./resolvers');
const SeriesAPI = require('./datasources/series.api')
const MoviesAPI = require('./datasources/movies.api')

...

const server = new ApolloServer({
  typeDefs,
  resolvers,
  dataSources: () => ({
    seriesAPI: new SeriesAPI(),
    moviesAPI: new MoviesAPI()
  })
});
```
{% endcode %}

