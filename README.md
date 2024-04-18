# pr-comment
Easy comment interaction with persistent key based comment tracking across
multiple runs. Upsert, post, delete comments.

# Usage
## Post a comment
To simply post a comment in the current PR. By default, the Pull Request ID is
determined automatically from the github context or an API call to retrieve
PRs associated with the commit.
```
- uses: direct-actions/pr-comment@v0.1.0
  with:
    comment: Comment from run ${{ github.run_id }}.
```

##  Post a comment with the option to update or delete it later.
For the ability to get, update or delete a comment later, simply add a key. The
key is embedded in an HTML comment, and will be hidden from view.
```
- uses: direct-actions/pr-comment@v0.1.0
  with:
    comment: Comment from run ${{ github.run_id }}.
    key: main-comment
```
Later on, in the same or a different run, workflow or job, the same comment can
be interacted with by specifying the same key.
```
- uses: direct-actions/pr-comment@v0.1.0
  with:
    comment: Updated comment from run ${{ github.run_id }}.
    key: main-comment
```
The default operation is `upsert` - which creates a new comment if a matching
comment does not exist, or update an existing comment if it does. Other
operations exist, like `delete`.
```
- uses: direct-actions/pr-comment@v0.1.0
  with:
    key: main-comment
    operation: delete
```

## Advanced usage - Regular Expressions
In addition to (or in place of) tracking a comment by a key, a comment can be
located with a [jq compatible regular expression (regex)](https://jqlang.github.io/jq/manual/#regular-expressions)
that is tested against the body of existing comments.
```
- uses: direct-actions/pr-comment@v0.1.0
  with:
    operation: delete
    regex: Created by GitHub Run [0-9]+
```
Regex flags can even be used for advanced regex matching including case
insensitive and multi-line modes.
```
- uses: direct-actions/pr-comment@v0.1.0
  with:
    operation: delete
    regex: created by github run [0-9]+
    regex_flags: im
```

## Advanced usage - Match type
When finding a previous comment based on author, key, and/or regex, a later
operation will typically operate on the last matching comment found. However,
this behavior can be changed to work on the first matching comment, or all
matching comments.
```
- uses: direct-actions/pr-comment@v0.1.0
  with:
    match: all
    operation: delete
    regex: Created by GitHub Run [0-9]+
```

## Advanced usage - commenting in other repository/pull requests
In some cases, commenting on a PR in a different repository and/or pull request
is desired. This can be useful when using triggers other than `on: pull_request`,
or when pull requesting from a forked repo. Note that when commenting in a
different repo, the default token will need to be overridden.
```
- uses: direct-actions/pr-comment@v0.1.0
  with:
    comment: Comment from ${{ github.repository }} run ${{ github.run_id }}.
    pull_request: 572
    repository: other-org/other-repository
    token: ${{ steps.get-token.outputs.token }}
```
