---
permalink: /electronjsfiles.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

## ElectronJS
For this post (though I have not written about ElectornJs before) I must assume that you are familiar. [Here](https://electronjs.org/docs/tutorial/about), go and have a loook if you must.

## Accessing local files
ElectronJS (via [Node API](Node](https://electronjs.org/docs/tutorial/application-architecture#using-nodejs-apis))s) gives you access to the local file sytem. This is a cross platform method and think of it like R, Python, Java or any other abstraction over the whole File System of the native OS.

This is achieved via the ["app"](https://electronjs.org/docs/api/app)'s [`getPath`](https://electronjs.org/docs/api/app#appgetpathname) API method. So for instance, to get to the user's "My Documents" on OSX or "Documents" on Windows, the signature to use would be `app.getPath('documents')`.

#### Just want an image for my img element

I know, it did take me a while to sort this out, hence my attempt here at making it really simple. Please be patient. So you should also know that you are allowed to use [Node](https://electronjs.org/docs/tutorial/application-architecture#using-nodejs-apis) API's.

So, how do I read an image from a local file path and set it to my `<img src="file/path/image.png" />`?

Long answer after the short answer: this is how you do it if the file was at your `My Documents/image.png`:

```js
// ES5
// https://www.w3schools.com/js/js_const.asp
const path = require('path');
const app = require('electron').remote.app;
const fs = require('fs');

// dir is users Documents in Windows and equivalent on Linux and OSX
const dir = app.getPath("documents")
// path is really handy for getting those forward and backward slashes right
const imagePath = path.join(dir, 'image.png')
// we check to make sure file exists
// then append a dataurl signature to the base64
// fileread (sync) from fs
const image = fs.existsSync(imagePath) &&
    'data:image/jpg;base64,' + 
    fs.readFileSync(imagePath, 'base64');

// finally
<img id="myImg" alt="gneeric alt" />
document.getElementById("myImg").src = image;
// or in jsx and React context
<img src={image} alt="appropriate alt" />
```

How did we do this? We read the file using Node's `fs` and turned the image into a [data URL](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs) (using the `data:` scheme) and that *can* be assigned to the `img` element.

It is not allowed to achieve this by providing local file path just as you cannot do it in a browser by providing the local path `/tmp/image.png`. Also we cannot use the [FileReader](https://developer.mozilla.org/en-US/docs/Web/API/FileReader) API going around "rooting" users's files. It can be done via the [dialog](https://electronjs.org/docs/api/dialog) API by asking the user to select it. 
