# Schema for mutation

An example client mutation request may look like this:

```graphql
mutation LoginUser {
  login(email: "test@mail.com", password: "123456") {
    jwt
    user{
      username
      email
    }
  }
}
```

By this mutation client sends login data to server\(email and password\) and expects to get defined response. Response fields can be a newly created fields, or existing fields. An example response may look like this:

```javascript
{
  "data": {
    "login": {
      "jwt": "asdfghjklqwertyu",
      "user": {
        "username": "test",
        "email": "test@mail.com"
      }
    }
  }
}
```

There is one important difference between Query and Mutation: ****Query fields are executed in parallel, mutation fields run in series, one after the other.  


