# Resolver implementation

To return paginated list we have to also refactor resolver, because data returned by resolver have to be compatible with schema.

```javascript
module.exports = {
	Query: {
		movies: async (_, { pageSize = 5, after }, { dataSources }) => {
			const allMovies = await dataSources.moviesAPI.getAllMovies();

			const items = paginateResults({
				after,
				pageSize,
				results: allMovies
			});
			return {
				edges: items,
				totalCount: allMovies.length,
				pageInfo: {
					cursor: items.length ? items[items.length - 1].cursor : null,
					// if the cursor of the end of the paginated results is the same as the
					// last item in _all_ results, then there are no more results after this
					hasNextPage: items.length
						? items[items.length - 1].cursor !== allMovies[allMovies.length - 1].cursor
						: false
				}
			};
		},
	}
}
```

This function fetches all available data first. It's not only one true approach, because it's possible to pass GraphQL query arguments into external service request to provide pagination. But, for this tutorial we want to keep things simple.

Next, function `paginateResult` will slice chunk of data based on `pageSize` and cursor determined by `after` . Below is an example on how this function may look like:

```graphql
module.exports.paginateResults = ({
	after: cursor,
	pageSize = 20,
	results,
	// can pass in a function to calculate an item's cursor
	getCursor = () => null
}) => {
	if (pageSize < 1) return [];

	results = results.map((item) => ({ node: item, cursor: item.cursor }));

	if (!cursor) return results.slice(0, pageSize);
	const cursorIndex = results.findIndex((item) => {
		// if an item has a `cursor` on it, use that, otherwise try to generate one
		let itemCursor = item.cursor ? item.cursor : getCursor(item);

		// if there's still not a cursor, return false by default
		return itemCursor ? cursor === itemCursor : false;
	});

	return cursorIndex >= 0
		? cursorIndex === results.length - 1 // don't let us overflow
			? []
			: results.slice(cursorIndex + 1, Math.min(results.length, cursorIndex + 1 + pageSize))
		: results.slice(0, pageSize);
};

```



