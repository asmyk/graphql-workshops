# Display items list on front end

To display data on frontend we need to tell GraphQL what query will be executed and what data we want. An example below shows a simple query that fetches a list with info about TV series:

{% code title="src/components/SeriesList.js" %}
```jsx
import React, { Fragment } from 'react';
import { useQuery } from '@apollo/react-hooks';
import gql from 'graphql-tag';

import { Header, Button, Loading } from '../components';

const GET_SERIES = gql`
    query seriesList() {
      series {
          name
          description
          id
      }
    }
`;
export default function SeriesList() {
  const { data, loading, error } = useQuery(GET_SERIES);
  if (loading) return <Loading />;
  if (error) return <p>ERROR</p>;

  console.log(data)

  return (
    <Fragment>
      <Header />
      {data.series &&
        data.series.series &&
        data.series.series.map(item => (
          <div>Name: {data.series.name}</div>
        ))}
    </Fragment>
  );
}
```
{% endcode %}

GraphQL Query is wrapped by function from `graphql-tag`. Then, we execute `useQuery` hook with query as an argument. It's a React Hook that fetches a GraphQL query and exposes the result so you can render your UI based on the data it returns.

**Parameterized Query**

Example below shows how to execute query with some parameters. In this case, component queries server for Movie object with given Id number.

```jsx
import React, { Fragment } from 'react';
import { useQuery } from '@apollo/react-hooks';
import gql from 'graphql-tag';

import { Loading, Header, LaunchDetail } from '../components';
import { ActionButton } from '../containers';

export const GET_MOVIE_DETAILS = gql`
  query MovieDetails($id: ID!) {
    movie(id: $id) {
      id
      name
      description
    }
  }
`;
export default function MovieDetailsPage() {
  const movieId = 434511;
  const { data, loading, error } = useQuery(
    GET_MOVIE_DETAILS,
    { variables: { id: movieId } }
  );
  if (loading) return <Loading />;
  if (error) return <p>ERROR: {error.message}</p>;

  return (
    <Fragment>
      <Header>
       {data.movie.name} - 
       {data.movie.description}
      </Header>
    </Fragment>
  );
}
```

## Fragments

Fragments are the primary unit of composition in GraphQL.

Fragments allow for the reuse of common repeated selections of fields, reducing duplicated text in the document. Inline Fragments can be used directly within a selection to condition upon a type condition when querying against an interface or union.

Let's refactor above example with Fragments functionality:

```jsx
 import React, { Fragment } from 'react';
import { useQuery } from '@apollo/react-hooks';
import gql from 'graphql-tag';

import { Loading, Header, LaunchDetail } from '../components';
import { ActionButton } from '../containers';

export const MOVIES_FRAGMENT = gql`
  fragment movieFields on Movie {
    id
    name
    description
  }
`
export const GET_MOVIE_DETAILS = gql`
  query MovieDetails($id: ID!) {
    movie(id: $id) {
      ...movieFields
    }
  }
${MOVIES_FRAGMENT}
`;
export default function MovieDetailsPage() {
  const movieId = 434511;
  const { data, loading, error } = useQuery(
    GET_MOVIE_DETAILS,
    { variables: { id: movieId } }
  );
  if (loading) return <Loading />;
  if (error) return <p>ERROR: {error.message}</p>;

  return (
    <Fragment>
      <Header>
       {data.movie.name} - 
       {data.movie.description}
      </Header>
    </Fragment>
  );
}
```

Now, newly created fragment can be reused here, and also in other components which fetch this kind of data.

