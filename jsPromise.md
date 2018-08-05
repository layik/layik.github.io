---
permalink: /jspromise.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

The name (Promise) is slightly confusing for the context but once clear in your head, it has been hard for me to think about a better name. You promise to get back to the caller with one of two `callback functions` (notice functions) in any case. To be more useful, you promise to return a "positive" result or a "negative" one. And "conventionally" two other terms are used: `resolve` and `reject`. These are nothing but placeholder terms and anything else could be used in their place. It is the order that matter not the terms.

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
             () => console.log("callbackTow")) //if not interested in the error, second one can be ignored, in this example this is actually null
// output 
// callbackOne
```
The reason is just order and nothing else because the first console statement is the one returned in the "then" function. I could not make sense of the NodeJS/V8 source code by a quick look but at least the [`enum`](https://github.com/nodejs/node/blob/master/deps/v8/src/objects/promise.h#L136) here also has the same order. 

Both functions (one/resolve, two/reject) are able to return any object desired. Usually with the resolve a useful object is returned. with the reject or in the case of throwing an error, the error value is returned. Here comes the important bit about promises: `then` prototype function or in the V8 code, thenable object.

The `promise.then` prototype function above, returns the `promise` itself with both callback functions. One of the two is always null, because it has not been called. To be pedantic, in the of the `resolve` or first callback, the second is `null` and vice versa. Here is the [docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) outlining `then` function:

```{js}
var promise = new Promise(function(resolve, reject) {
  resolve('Success!'); //here we call the resolve without any delay
});

promise.then(function(value) {
  console.log(value);
  // expected output: "Success!"
}); //notice we dont even pass a second function, we know above we called "resolve" or the first callback.
```
The docs in `then` outlines order and how handlers (callbacks) are called, for the sake of similicity I am going to ignore this.

Finally, and perhaps crucially, these two callback functions are different from the methods [`reject`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject) and [`resolve`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve) which both return a promise which has been "resolved" with a value or "rejected" with a reason respectively. 


last updated: 5th Aug 2018 - 23:10 BST
