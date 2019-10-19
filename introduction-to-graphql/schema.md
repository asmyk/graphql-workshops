# Schema

GraphQL schema is at the core of any GraphQL server implementation. Query schema is similar to JSON file, but it's a query language. When client wants to ask server for some data, basic schema may look like this:

{% code-tabs %}
{% code-tabs-item title="Client send this query to GraphQL server" %}
```graphql
{
  users {
    name
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

And in the response client will receive something like this:

```javascript
{
  "data": {
    "users": [
      { "name": "John" },
      { "name": "Donald" },
      { "name": "Tom" }
    ]
  }
}
```

This is how basic schema look like. We will go into details in next sections.

## Architecture

Every query between client and server is sent by HTTP Post method. Compared to REST API, GraphQL doesn't use  different HTTP methods for data fetch/update/create. All communication is handled by Query passed in Post body.



![source: https://www.howtographql.com](../.gitbook/assets/image%20%287%29.png)

