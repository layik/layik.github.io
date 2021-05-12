---
permalink: /sharedjs.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

13th July 2018

# Share files between R & JS
Recently have been working on [att](https://github.com/ATFutures/activeTransportToolbox), where I had to share some JS lists with the R serverside code. I found out that R [V8](https://github.com/jeroen/V8) package is obviously not able to run in the browser so things cannot be as rosey as they are in Node. Here is how it worked:

``` js
//Shared.js file
global.list = [1, 2, 3]
```

The R V8 package, without other libraries is not able to use CommonJS (modules.exports) keywords. That is why we use the `global` keyword. I found out it is shared between the V8 engine and the React based code (Node environment).

``` r
# plumber.R file
ct <- V8::v8()
ct$source('Shared.js'))
list <- ct$get("global.list")
# [1] 1 2 3
```
We cannot use import as `Shared.js` does not use modules.
``` js
//test.js
require('./Shared.js')
console.log("global.list")
[ 1, 2, 3 ]
```
