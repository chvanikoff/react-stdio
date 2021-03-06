# react-stdio [![npm package][npm-badge]][npm]

[npm-badge]: https://img.shields.io/npm/v/react-stdio.svg?style=flat-square
[npm]: https://www.npmjs.org/package/react-stdio

[react-stdio](https://github.com/mjackson/react-stdio) lets you render [React](https://facebook.github.io/react/) components on the server, regardless of the backend technology you're using.

As its name suggests, other processes communicate with react-stdio using standard streams. The protocol is JSON, so any environment that can spawn a child process and write JSON to its stdin can use the server. Requests are handled serially, so responses are issued in the same order requests are received.

## Installation

Using [npm](https://npmjs.com):

    $ npm install -g react-stdio

## Usage

Once you've installed the server, you will have a `react-stdio` binary available (assuming `node_modules/.bin` is in your `$PATH`). Execute it to start the server.

To render a React component, write a JSON blob to stdin with any of the following properties:

    component       The path to a file that exports a React component (required)
    props           Any props you want to pass to the component (optional, default is {})
    render          The type of rendering (optional, default is renderToString)

If the request is successful, the server will put a JSON blob with `{"html":"..."}` on stdout. If the request fails for some reason, the JSON will have an `error` property instead of `html`.

Example:

    $ echo '{"component":"./MyComponent","props":{"message":"hello"}}' | react-stdio

If you'd like to use a render method other than [`renderToString`](https://facebook.github.io/react/docs/top-level-api.html#reactdomserver.rendertostring) or [`renderToStaticMarkup`](https://facebook.github.io/react/docs/top-level-api.html#reactdomserver.rendertostaticmarkup) you can pass a path to a file that exports your rendering function. The signature of your `render` function should be:

```js
function render(element, callback) {
  // ...
}
```

This function is asynchronous so you have time to do data fetching before you render if you wish. Call `callback(error, html)` when you're finished.

## Environment

Your component file is loaded in a vanilla node.js environment. If you need additional code transforms to run (e.g. using webpack or Browserify) you should create your bundle first and tell react-stdio to load your bundle instead of the plain component file. If you're using webpack to build your bundle, you'll want to use `"libraryTarget": "commonjs2"` in your config so the bundle exports the component using `module.exports = MyComponent`.

Also, since react-stdio uses the `stdout` stream for all program output, all writes your code makes to `process.stdout` (including `console.log` statements) are redirected to `process.stderr`.

## Integrations

- [Elixir/Phoenix](http://blog.overstuffedgorilla.com/render-react-with-phoenix/)
- [Ruby on Rails](https://github.com/aaronvb/rails_react_stdio)

If you'd like to add an integration here, please submit a PR.
