---
title: GitHunt Example and Schema
---

In the documentation we'll show examples of using Apollo in React via the GitHunt example application.

GitHunt is a Product Hunt-style application that shows a list of GitHub repositories, sorted by votes with attached comments. [The API server code](https://github.com/apollostack/GitHunt-API) demonstrates combining two data sources--a third-party API and a local database--in a single GraphQL endpoint.

The [React UI](https://github.com/apollostack/GitHunt-React), which we'll focus on in this guide, shows a lot of the techniques that you can use to build a great React UI for *any* GraphQL server. You can check out the full source code on GitHub or just read the snippets in the guide.

<h2 id="githunt-schema">The GitHunt Schema</h2>

Most of the examples in this guide are written for the GitHunt schema. You can see the full schema written in the [GraphQL Schema Language](https://wehavefaces.net/graphql-shorthand-notation-cheatsheet-17cd715861b6) below:

```graphql
# This uses the exact field names returned by the GitHub API for simplicity
type Repository {
  name: String!
  full_name: String!
  description: String
  html_url: String!
  stargazers_count: Int!
  open_issues_count: Int

  # We should investigate how best to represent dates
  created_at: String!

  owner: User
}

# Uses exact field names from GitHub for simplicity
type User {
  login: String!
  avatar_url: String!
  html_url: String!
}

type Comment {
  postedBy: User!
  createdAt: Float! # Actually a date
  content: String!
  repoName: String!
}

type Vote {
  vote_value: Int!
}

type Entry {
  repository: Repository!
  postedBy: User!
  createdAt: Float! # Actually a date
  score: Int!
  comments: [Comment]! # Should this be paginated?
  commentCount: Int!
  id: Int!
  vote: Vote!
}

# To select the sort order of the feed
enum FeedType {
  HOT
  NEW
  TOP
}

type Query {
  # For the home page, the offset arg is optional to get a new page of the feed
  feed(type: FeedType!, offset: Int, limit: Int): [Entry]

  # For the entry page
  entry(repoFullName: String!): Entry

  # To display the current user on the submission page, and the navbar
  currentUser: User
}

# Type of vote
enum VoteType {
  UP
  DOWN
  CANCEL
}

type Mutation {
  # Submit a new repository
  submitRepository(repoFullName: String!): Entry

  # Vote on a repository
  vote(repoFullName: String!, type: VoteType!): Entry

  # Comment on a repository
  submitComment(repoFullName: String!, commentContent: String!): Comment
}
```
