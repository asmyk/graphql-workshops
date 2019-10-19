# Benefits

1. No more under-fetching It happens when single endpoint doesn't return enough informations, and client have to ask another service for additional data. For example we want to display list with user names who liked some post. First, we get some data about entry by calling `api/post-entry/{id}` and in response there is info about user id's: `{   likedBy: [122,3442,522,333] }`  and in the end client have to hit `api/user/{id}` for each user.
2. No more over-fetching

   Imagine that app needs to display list only with user names. So, client needs to ask `api/users` to get users list. But, this API will return bunch of data for each user, and client needs only username. 

3. Fetch exactly what client wants For every query GraphQL will deliver everything that client ask. Not more, not less. Client will get always predictable results in response.
4. Easy co-operation with backend

   Client and server have to agree for one schema. To connect client with backend engineers should agree about schema definition. Also, updating schema is very easy. By using a single evolving version, GraphQL APIs give apps continuous access to new features and encourage cleaner, more maintainable server code.

