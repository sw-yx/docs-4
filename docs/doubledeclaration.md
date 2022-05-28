---
id: declarationdeclaration
title: The Double Declaration Problem
---

> **obligatory note**: babel-blade is not yet production ready! Please proceed only if you are an early adopter. Feel free to chat with [@swyx](https://twitter.com/swyx) or [check our issues!](https://github.com/sw-yx/babel-blade/issues/)

## What is the double declaration problem?

Simply, it is the bad developer experience of

- having to declare what you want to query in the GraphQL template string
- and then again when you are using the data in your application.
- Curly braces also have to match up.

When you don't do it just right:

- Ommissions are confusing to debug and
- overfetching due to stale queries is also a problem.

> 2022 Edit:
>
> See other commentary on Double declaration:
> - https://xuorig.medium.com/gqless-or-gqlful-ec36fe588a4b
> - https://wundergraph.com/blog/solving_the_double_quintuple_declaration_problem_in_graphql_applications_how_to_not_repeat_yourself

## Problem Statement

Here is a typical graphql query using [urql](https://codesandbox.io/s/p5n69p23x0) (taken [straight from urql's docs](https://github.com/FormidableLabs/urql#getting-started)):

```jsx
import { Connect, query } from 'urql';

const QueryString = `
  query Movie($id: String) {
    movie(id: $id) {
      id,
      title,
      description,
      genres,
      poster {
        uri
      }
    }
  }

`;

const Movie = ({ id, onClose }) => (
  <div>
    <Connect
      query={query(QueryString, { id: id })}
      children={({ loaded, data }) => {
        return (
          <div className="modal">
            {loaded === false ? (
              <p>Loading</p>
            ) : (
              <div>
                <h2>{data.movie.title}</h2>
                <p>{data.movie.description}</p>
                <button onClick={onClose}>Close</button>
              </div>
            )}
          </div>
        );
      }}
    />
  </div>
);
```

You see how `title` and `description` are specified twice, while `poster` and `genre` aren't even used.
