# Benefits

1. No more under-fetching It happens when a single endpoint doesn't return enough information, and client has to ask another service for additional data. For example we want to display a list with user's names who have liked some post. Firstly, we get some data about an entry by calling `api/post-entry/{id}` and in a response there is info about user's ids: `{   likedBy: [122,3442,522,333] }`  and in the end client has to hit `api/user/{id}` for each user.
2. No more over-fetching

   Imagine that the app needs to display a list only with user's names. So, a client needs to ask `api/users` to get users' list. But, this API will return a bunch of data for each user, and a client only needs a username. 

3. Fetch exactly what client wants For every query GraphQL will deliver everything that client askes. Not more, not less. Client will get always predictable results in response.
4. Easy co-operation with backend

   Client and server have to agree for one schema. To connect client with backend, engineers should agree about a schema definition. Also, updating schema is very easy. By using a single evolving version, GraphQL APIs give apps continuous access to new features and encourage cleaner, more maintainable server code.

