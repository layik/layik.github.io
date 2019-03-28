---
permalink: /htmljsx.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

[This](https://raw.githubusercontent.com/reactjs/reactjs.org/master/static/html/single-file-example.html) example from React docs shows how to use JSX in the browser:

```
<!DOCTYPE html>
..
    <!-- Don't use this in production: -->
    <script src="https://unpkg.com/babel-standalone@6.15.0/babel.min.js"></script>
  </head>
  <body>
    <div id="root"></div>
    <script type="text/babel">

      ReactDOM.render(
        <h1>Hello, world!</h1>,
        document.getElementById('root')
      );

    </script>
    <!--
      Note: this page is a great way to try React but it's not suitable for production.
      It slowly compiles JSX with Babel in the browser and uses a large development build of React.

      Read this section for a production-ready setup with JSX:
      https://reactjs.org/docs/add-react-to-a-website.html#add-jsx-to-a-project

      In a larger project, you can use an integrated toolchain that includes JSX instead:
      https://reactjs.org/docs/create-a-new-react-app.html

      You can also use React without JSX, in which case you can remove Babel:
      https://reactjs.org/docs/react-without-jsx.html
    -->
  </body>
</html>
```
The notes there are what I want to discuss a little here. You can certainly include it in the HTML file using `<script type="text/babel">` with the attribute `text/bable` and obviously you would then need the Babel `.js` file (minified or not transpiler).

Now, can we take the JSX out into a separate file and include it in the HTML using the same or standard `<srcipt src='react.js'>`? I was not able to.

The only way I have found to do this, is to have a precompiled version of the code in ES as stated by the [React docs](https://reactjs.org/docs/add-react-to-a-website.html), and import into the browser with a standard `<script src='react_no_jsx.js'>`. Here is what the `Hello World` React would would be transpiled.
 
<img width="1055" alt="Screenshot 2019-03-28 at 17 55 56" src="https://user-images.githubusercontent.com/408568/55181067-c4bf9900-5182-11e9-860f-e2f9ee545483.png">


So:

```html
<!--index.html-->
  <body>
    <div id="container"></div>
    <script src='react.js'></script>
  </body>
```

```js
//react.js
ReactDOM.render(React.createElement(
  'h1',
  null,
  'Hello, world!'
), document.getElementById('root'));
```

Maybe the reason why the JSX tags within a separate file is not "compiled" by the Babel compiler is just context, maybe not. I could not be sure so far. I would love to update this post with a definite answer.