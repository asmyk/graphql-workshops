# What is mutation?

Where queries fetch data, mutations are responsible for making changes to data. Mutations rely on a similar syntax to queries.

### Schema

Just like in queries, if the mutation field returns an object type, you can ask for nested fields. This can be useful for fetching the new state of an object after an update. Let's look at a simple example mutation defined in server:

```graphql
type Mutation {
    login(email: String, password: String): UserLoginResponse
}

type UserLoginResponse {
    jwt: String!
    user: User
 }
```

This mutation example expect to get `email` and `password` fields, and in response returns object contains some token and User object.

### Resolver

Resolver for mutation looks very similar to query resolvers:

{% code title="src/resolvers.js" %}
```javascript
Mutation: {
	login: async (_, { email, password }, { dataSources }) => {
		const user = await dataSources.userAPI.login({ email, password });
		return user;
	}
}
```
{% endcode %}

We defined login field resolver, which is a `async` function that returning mapped response from external service. Example of data source may look like this:

```javascript
class UserAPI extends RESTDataSource {
	constructor( ) {
		super();
		this.baseURL = 'http://myusersservice.com/';
	}
	
	async login({ email, password } = {}) {
		return	this.post('auth/local', {identifier: email, password });
	}
}
```



