# Add optimistic update in UI client

Optimistic UI is a pattern that you can use to simulate the results of a mutation and update the UI even before receiving a response from the server. Once the response is received from the server, the optimistic result is thrown away and replaced with the actual result.

Let's look at this simple scenario where we can apply the optimistic update. We have a list with comments, and a form for post a new comment. When a user posts a new comment, then we need to update a list before server responds with data. Keep in mind that optimistic result doesn't have to be full data object - data will be corrected after server response

```javascript
const SUBMIT_COMMENT_MUTATION = gql`
  mutation SubmitComment( $content: String!) {
    submitComment(
      content: $content
    ) {
      author
      createdAt
      content
    }
  }
`;

function CommentsList({ currentUser, postId }) {
  const [mutate] = useMutation(SUBMIT_COMMENT_MUTATION);
  return (
    <Comments
      submit={(commentContent) =>
        mutate({
          variables: {commentContent },
          optimisticResponse: {
            __typename: "Mutation",
            submitComment: {
              __typename: "Comment",
              author: currentUser,
              createdAt: new Date(),
              content: commentContent
            }
          },
          update: (proxy, { data: { submitComment } }) => {
            // Read the data from our cache for this query.
            // When particular post is fetched by id, we have to pass it here also
            const data = proxy.readQuery({ query: CommentsQuery, variables: { postId });
            // Write our data back to the cache with the new comment in it
            proxy.writeQuery({ query: CommentsQuery, data: {
              ...data,
              comments: [...data.comments, submitComment]
            }});
          }
        })
      }
    />
  );
}
```

