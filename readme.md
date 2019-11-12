## @captaincodeman/router

![typescript](https://badgen.net/badge/icon/typescript?icon=typescript&label)
![types included](https://badgen.net/npm/types/tslib)
![normal size](https://badgen.net/badgesize/normal/CaptainCodeman/js-router/master/dist/router.min.js)
![gzipped size](https://badgen.net/badgesize/gzip/CaptainCodeman/js-router/master/dist/router.min.js)
![brotli size](https://badgen.net/badgesize/brotli/CaptainCodeman/js-router/master/dist/router.min.js)

An ultra-tiny client-side router for modern pregressive web apps.

This tiny module exports a single function that takes an object of url patterns and returns a function that can be called to get the match, based on the url. It's all I need for routing and helps when trying to reduce the size of client apps.

**routes.js**

```js
import createMatcher from '@captaincodeman/router'
import homeView from './views/home'
import todoListView from './views/todo-list'
import todoDetailView from './views/todo-detail'
import articleView from './views/article'

export default createMatcher({
  '/': homeView,
  '/todos': todoListView,
  '/todos/:id': todoDetailView,
  '/article/*': articleView
})
```

This returns a function that can be called to retrieve the value, along with extracted parameters:

**other.js**

```js
import routeMatcher from './routes'

// call it with a pathname you want to match
routeMatcher('/')
// =>
// {
//   page: homeView,
//   params: { }
// }

routeMatcher('/todos')
// =>
// {
//   page: todoListView,
//   params: { }
// }

routeMatcher('/todos/41237')
// =>
// {
//   page: todoDetailView,
//   params: {
//     id: '47'  
//   }
// }

routeMatcher('/articles/some/other/path')
// =>
// {
//   page: articleView,
//   params: {
//     path: 'some/other/path'
//   }
// }

routeMatcher('/not-a-page')
// =>
// null
```

Note that a failed match returns null, allowing you to provide whatever fallback you want (such as a 'page not found' view)

## why is this useful?

In a Single Page App you'll need to determine what should be displayed based on the current url. Part of the URL will determine the view to show and part will be parameters that determine which data should appear in that view. This package makes it easy to parse and extract both of those.

If you are using [redux](http://redux.js.org/) for application state then there is a good argument for making the routing system part of that. Decisions such as which data to fetch can then be part of the application state instead of residing in UI components - that can improve performance as data can be pre-fetched to reduce letency instead of waiting for an empty view to load and render before requesting it.

## how parameter extraction works

### parameters

Use a `:name` format for parameters:

pattern: `'/todos/:id'`
url: `'/todos/123'`
extracted params: `{ id: '123' }`

Multiple parameters and static segments can be mixed:

pattern: `'/topic/:topic/post/:post'`
url: `'/topic/123/post/456'`
extracted params: `{ topic: '123', post: '456' }`

Parameters can be made optional:

pattern: `'/topic/:topic(/post/:post')`
url: `'/topic/123'`
extracted params: `{ topic: '123' }`

Wildcards capture the remainder of the match into a param called `path`

pattern: `/blog/*`
url: `'/blog/2019-11-12/how-to-use-client-routing'`
extracted params: `{ path: '2019-11-12/how-to-use-client-routing' }`

## notes

Things to be aware of...

1. Order is important, the first match wins
2. If you re-use parameter names in the url pattern they'll be overwritten in the result.
3. If you need query string values, match the base url first with this module, then use the browser's built-in [`URLSearchParam`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) to parse them

## install

    npm install @captaincodeman/router --save

## credits

This was based on [feather-route-matcher](https://github.com/HenrikJoreteg/feather-route-matcher) by [HenrikJoreteg](https://github.com/HenrikJoreteg) which itself borrows a few extremely well-tested regexes from Backbone.js to do its pattern matching. I converted it to TypeScript, fixed a couple of quirks with pattern matching and shaved off a few bytes from the size. Thanks for the generous licensing!

## building

Build library using

    npm run build

## tests

Run unit tests using

    npm run test
