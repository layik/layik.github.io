---
permalink: /jspromise.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

I must have read quite a few articles on promises and as mentioned by a lot of people. You usually use promises before you actually write one. In fact, this use rather than writing one made me more rely on what others had written rather than write my own and rely on my own code.

Promises are great, the name is slightly confusing for the context but once clear in your head, it is hard to find a better name. You promise to get back to the caller with one of two `callbacks` in any case. To be more useufl, you promise to return a "positive" result or a "negative" one. And "conventionally" two other terms are used: `resolve` and `reject`. These are nothing but placeholder terms and anything else could be used in their place. It is the order that matter not the terms.

Here is the bit that made it so confusing for me, there is nothign in the naming except the `Promise` object. The rest, is just about order of the parameters of the function. So when the [docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) define promise as

```{js}
var promise = new Promise(function(resolve, reject) {
  setTimeout(resolve, 100, 'foo');
});
```
that is the same as 
```{js}
var promise = new Promise(function(callbackOne, callbackTwo) { // initialization using `new` keyword
  setTimeout(callbackOne, 100, 'foo'); //this is to simulate a delay
});
```
In both above chunks, the caller using 
```{js}
promise.then(() => console.log("callbackOne"), //only this one is called 
             () => console.log("callbackTow")) //if not interested in the error, second one can be ignored.
// output 
// callbackOne
```
The reason is just order and nothing else because the first console statement is the one returned in the "then" function. I could not make sense of the NodeJS/V8 source code by a quick look but at least the [`enum`](https://github.com/nodejs/node/blob/master/deps/v8/src/objects/promise.h#L136) here also has the same order. 

Both functions (one/resolve, two/reject) are able to return any object desired. Usually with the resolve a useful object is returned. with the reject, usually an error is returned.
