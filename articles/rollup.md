---
permalink: /rollup.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

12th May 2021

* [Who should read this?](#Who-should-read-this?)
* [Do not ctrl + c this](#Do-not-ctrl-+-c-this)
* [What you will learn](#What-you-will-learn)
* [The rollup.config.js file](#Please-show-me-your-rollup.config.js)
* [Conclusion](#Conclusion)

# Rolling up a React Component Library
This article is an attempt at helping you bundle together your React (with JSX) component library as a single file to use in HTML files using [`rollupjs`](https://rollupjs.org/guide/en/).

## Who should read this?
If you are here looking to [`rollup`](https://rollupjs.org/guide/en/) your React component library or some other JS `library` then I hope it this will be useful or at least encouraging read. I learned what is in this article from efforts of trying to rollup a React component library namely: [Turing Geovisualization Engine](https://github.com/layik/eAtlas/tree/npm) - TGVE or eAtlas for short. If you are just starting to learn about bundlers or rollup itself, this might not be a good place to be.

## Do not ctrl + c this

> First of all, remember that your project is going to be different to the [TGVE](https://github.com/layik/eAtlas/tree/npm) and therefore, you would need to work out your requirements. 

This is the most important takeaway of this post and this is why I am writing this entry. There is no recipe to bundle your application just like X app unless your application is like X app.

In that regard, a starter example will be useful of course but would be different. The fact that rollup is also "fiddly" to an extent but I found it much more straightforward than [webpack](https://webpack.js.org), I hope becomes clear if you continue working on your own [`rollup.config.js` file](https://rollupjs.org/guide/en/#configuration-files) and throughout this article.

## What you will learn
Here is what you can learn from this post:

1. Your project might be like no other and you *may* have unique requirements.
2. A rollup script successfully bundling your app does not necessarily mean you can now just add a `<script>` tag to any HTML file and your library will now run as a standalone script. This depends entirely on your dependencies. If you do not have any dependencies, then it *should*.
3. A fat standalone application is acceptable. Look at any RevealJS or similar presentation apps and you would agree that some 14MB file is not the end of the world.
4. Some fiddly parts of [rollupjs](https://rollupjs.org) that can generate a bundle.
5. Testing the results of your efforts.

So we can safely skip (1) without any less emphasis on its importance to remember. As for (2), I hope you can see from the TGVE case, it is better to include some of the dependencies. And then for (3), well I will leave you with that one to think about as there little we can do about it. For (4) and (5) we will take them one at a time, first let me have a go at (4).

## Please show me your rollup.config.js
Now then, have a look at this `rollup.config.js` file and let us discuss each of the lines, feel free to just copy and paste it but honestly, that is not what you should be doing. Come back when you have time to read and relax, then you can run your rollup.

```js
import babel from '@rollup/plugin-babel';
import resolve from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import scss from 'rollup-plugin-scss';
import json from '@rollup/plugin-json';
import image from '@rollup/plugin-image';

import pkg from './package.json';
import deckpkg from './node_modules/deck.gl/package.json';

// 1
const extensions = ['.js', '.jsx'];
// 2
const EXTERNALS = Object.keys(pkg.dependencies)
  .concat([
    'react', 'react-dom',
    'qs',
    '@luma.gl/engine',
    '@luma.gl/webgl',
    'baseui/a11y',
    'baseui/accordion'
  ])
  .concat(Object.keys((deckpkg && deckpkg.dependencies) || {}));

export default [{
  // 3
  input: 'src/index.js',
  // 4
  output: {
    // 5
    intro: 'const ENVIRONMENT = "production";',
    // 6
    format: "cjs",
    // 7
    file: "dist/eatlas.js",
    // 8
    name: 'eatlas'
  },
  // 9
  watch: {
    exclude: 'node_modules/**'
  },
  plugins: [
    // 10
    resolve({
      skip: EXTERNALS,
      mainFields: ['module', 'main', 
      'jsnext:main', 'browser'],
      extensions
    }),
    // 11
    babel({
      babelHelpers: 'bundled',
      exclude: './node_modules/**',
      presets: ['@babel/env', '@babel/preset-react'],
      extensions,
    }),
    // 12
    commonjs(),
    scss(),
    json(),
    image()
  ],
  // 13
  external: EXTERNALS.concat(["lodash", "polished", "underscore"])
}];
```
Let us go through these lines one at a time. The whole thing happens when one understands these fiddly tools. 

### Input
1. Rolling up means identifying your JS source and that starts with an extension. As eAtlas is not a `.ts` project, I do not need anything other than `.js` and `.jsx`. 

2. Externals is your most important work. As stated above, to achieve your "single file" aim, one must start with the package dependencies and find out what is available as single file already. Everything else must be rolled up with your package. Needless to say, you can of course package ONLY your code and ask users to use the rolled up file in another npm package where dependencies can easily be met. That was my starting point.

For instance, Uber's `baseweb` package, as of now, could not be located as a [scoped]() single file, therefore, if we need to roll up eAtlas we must not exclude them. In the above config, it is, this is done on purpose. Hence the learning outcome number (2) above. 

> Adding dependency list from your `package.json` may not be picked up by `rollup`. 

This is because of the way npm packages can be defined. Again in the case of `baseui` or `baseweb` scoped packages, we import `baseui/button` but the dependency only says `baseui`. Therefore, rollup cannot detect them and that meant, I had to write a little script to get the list of the directories under `node_modules/baseui` to pass it to the object here. It is clipped for this article otherwise the list is "big".

3. Some would be hesitant to say this, but if you have the right `plugin` then your source should be your entry not some `build` folder or something else.

### Output
4. This is where you choose the world of JS. A CommonJS output looks like following:

```js
output: {
    // 5
    intro: 'const ENVIRONMENT = "production";',
    // 6
    format: "cjs",
    // 7
    file: "dist/eatlas.js",
    // 8
    name: 'eatlas'
  },
```
5. See documentation for the `production` mode, not interesting at all.
6. You have few options here, it would need a chapter to explain but I keep it to CommonJS.
7. The destination to where and what the output should be. What you really care about amongst all this work.
8. This is the name of your library.
9. Honestly, why is this even defined in RollupJS?

### Plugins
10. If you do not do this, rollup cannot locate a simple `require('lodash')` statement. I am amazed to say the least.
11. All right, we are dealing with React and JSX, so we need `babeljs` to transpile it back to browser readable JS. 

```js
// 11
    babel({
      babelHelpers: 'bundled',
      exclude: './node_modules/**',
      presets: ['@babel/env', '@babel/preset-react'],
      extensions,
    }),
```
Honestly, giving out a warning saying `You must explicitly say "babelHelpers: 'bundled'"` is something I would not be doing but I am sure RollupJS guys have good reason for it. I might revisit this and provide that good(?) reason. Making sure we exclude the node packages (did we not do that elsewhere?). The presets are the critical bits, you probably know this already. As for our extensions, I thought Rollup can pick it up from `resolve` (10)?

12. Great, almost there. Just remember that we are still defining plugins for Rollup. 
```js
  plugins: [
  //...
  // 12
    commonjs(),
    scss(),
    json(),
    image()
```
We were building `cjs` right? That is the first plugin. The next one `scss` just wraps any css and places them in a file with the same name as the output adding `.css` to it. If during rollup the script comes across a plain `json` file, it will break and say please give me the instructions. That is also true for images. They will not be touched if you use this script and you would have to copy over images used in your React components. I know, how painful is that?

13. Finally what we want to keep out and make sure are not rolled up with our code. Leave it out if you want to see a fat bundle. Why have you suddenly done some more `concat`s you want to shout at me? You are right but no need to present a perfect article right?

```js
{
  //...
  // 13
  external: EXTERNALS.concat(["lodash", "polished", "underscore"])
}
```

### Results
Here is what you would be seeing in your `not uglified` bundle if you have a look:

```js
// bundle.js
'use strict';
// sounds familiar (see (5) above)
const ENVIRONMENT = "production";

// list of externals
var React = require('react');
var styletronReact = require('styletron-react');
var baseui = require('baseui');
var styletronEngineAtomic = require('styletron-engine-atomic');
var DeckGL = require('deck.gl');
var MapGL = require('react-map-gl');
var centroid = require('@turf/centroid');
// .... so many more lines
```
As you can see, without any of the above externals, the script will return and ask the user to get those packages into scope.

The bundled `css` would also be done, in this case, separately in a file with the same name `eatlas.css` and would contain all css found by `rollup` and simply concatenated. This, I cannot point you to source code, would be some traversal of the files in your codebase starting with top level `css` file found. 

## Testing it
So that leaves us with testing the output. Let us imagine that like me, you also put everything in the externals and must install all the dependencies. I literally copied over the library's dependencies to a test application and installed them.

```sh
npx create-react-app test-app
cd test-app
vi package.json
# add your deps to the dependencies
yarn
```

Then in the `App.jsx`, if you have copied over the output from your `rollup.js` to a path where you can import it, just go `import Eatlas from './eatlas.js'` (assuming same directory path). Then you *should* be able to run it just like having installed via `yarn add eatlas` or linked it from the original project.

That also means in our case, where we rollup only our code and everything else is external, the package json of `test-app` and the original project `eatlas` would have to be the same. In addition ot any other peer-dependencies and what not. 

Another caveat as mentioned above, due to the way this rollup script works, we would also have to make sure that the relative paths of images must be the same. In this case, we have the entire JS code running from a file called `eatlas.js`, that is the library. So, any image which used to be called from `../../img` must now be called from the same location. Repeat, it is possible and will update this article when I know more, to bundle images just like we do in RevealJS etc.

## Conclusion
I can say without any hesitation that both webpack and the `react-scripts` are both [fiddly](https://dictionary.cambridge.org/dictionary/english/fiddly) to say the least, the latter of which is built on the [former](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/package.json#L86). But the criticism is a little unfair in the context of JavaScript (JS), I mean we should find a specific word to describe JS much stronger than fiddly. 


