# Handling user facing errors

Basically there are two types of errors:

* Controlled errors - those errors indicates that user did something wrong \(wrong long credentials, form field validation etc.\)
* Uncontrolled errors - those errors indicates that something bad happen\(network error, authentication failure, forbidden etc.\)

### Controlled errors

Controlled errors should be returned along with data in response. Example response looks like this

```graphql
{
  "errors": [
    {
      "message": "Given name is already used.",
      "code": "ALREADY_EXIST",
      "locations": [ { "line": 6, "column": 7 } ],
      "path": [ "user", "name" ]
    }
  ],
  "data": {
      # Available data here
  }
}
```

Those kinds of errors should be included in schema, and also resolvers should handle them. An example resolver may look like this:

```javascript
async (_, { email, password, username }, { dataSources }) => {
	try {
		const user = await dataSources.userAPI.register({ email, password, username });
		return user;
	} catch (error) {
		const parsedErrors = myFunctionThatParseErrorToJson()
		return {
			data: {
				email,
				password,
				username
			},
			errors:parsedErrors
		}
	}
},
```



