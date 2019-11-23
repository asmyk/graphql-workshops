# Connect schema with UI

For mutations Apollo Client gives us a `useMutation`hook.

Let's look at the example below:

```jsx
import { useMutation } from '@apollo/react-hooks';

export default function Login() {
  const [login, { data }] = useMutation(LOGIN_USER);
  return <LoginForm login={login} />;
}
```

This hook is similar to useQuery, but there are two differences:

1. The first value in the `useMutation` result tuple is a **mutate function** that actually triggers the mutation when it is called. 
2. The second value in the result tuple is a result object that contains loading and error state, as well as the return value from the mutation. 

One more step to run this request from client is to add mutation definition and wrap it into graphql-tag:

```jsx
import React from 'react';
import { useMutation } from '@apollo/react-hooks';
import gql from 'graphql-tag';

const LOGIN_USER = gql`
  mutation login($email: String!, $password: String!) {
    login(email: $email) {
      jwt
      user{
        username
        email
      }
    }
  }
`;

export default function Login() {
  const [login, { data }] = useMutation(LOGIN_USER);
  return <LoginForm login={login} />;
}
```

