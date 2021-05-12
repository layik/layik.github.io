---
permalink: /ghpages.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

22nd Mar 2021

# Deploying a React app on GH pages

1. [Introduction](#introduction)
2. [Setting up CI](#Setting-up-CI)
3. [Setting up the React app itself](#Setting-up-the-React-app-itself)
4. [Adding data](#Adding-data)
5. [Adding social meta tags](#Adding-social-meta-tags)
## Introduction

GitHub is evolving rapidly and things change faster than many of us can keep up with it. One of the changes is github pages. For instance, it used to restrict developers to a particular folder say `docs` but nowadays we have a little more flexibilty (see below). 

Therefore, there are few tricky bits about gh-pages itself and deploying a React app, or any simple React app that requires this blogpost. If the app in question has a backend like the example for this blog post (Turing Geovisualization Engine - TGVE), then we ignore the backend. Needless tosay, for now, though things might change quickly on GH, the backend is out of the question and we cannot have access to processes on GH servers.

In a nutshel, we need to make sure the ROOT URL of our deployment points to the `build` of the React app. So, here are the steps you need to take to deploy your app, or this is how the TGVE has been deployed.

## Setting up CI
One of the recent goodies from GH is GH [Actions](https://layik.github.io/ghactions). [This simple](https://github.com/layik/eAtlas/blob/master/.github/workflows/gh-pages.yml) `yml` file can get your React app (created using `react-scripts` or `create-react-app`) on a dedicated but orphan branch without messing with your other branches:

``` yml
... other setup
git config user.name "layik" 
git config user.email "l***@gmail.com"
git checkout --orphan gh-pages
git --work-tree build add --all
git --work-tree build commit -m 'Deploy'
git push origin HEAD:gh-pages --force
```

Full file can be found here, but the important lines are the ones above. In a new space, one needs to configure the git credentials first. Then, we checkout a branch from the clone. Then, we assign the `build` folder as the working-tree in git, read more here, and commit our changes. Finally, simply push it to our gh-pages branch and keep it (in my case) as a clean one commit head.

Just to be clear, these lines make sure that the `build` folder which in my case being the default build folder from `react-scripts build` command is the only output placed on branch `gh-pages`.

This is in contrast to considerations of publishing the app as an npm package and then using the package in your favourite github pages HTML file. I know this sounds more complicated but thought I should cover it as food for thought. Publishing a React app asn npm package itself has tricky bits. 

The TGVE is a geoplumber app and in the Dockerbuild exactly the same method is used by making use of the `build` as it should be.

## Setting up the React repo
To setup the repo, we need to make sure that the GitHub pages is set to an orphan branch called "gh-pages" and that we make "root" as the root directory to serve our app.

<img width="100%" style="border: 1px grey dashed" alt="Screenshot 2021-01-22 at 11 36 50" src="https://user-images.githubusercontent.com/408568/105486297-2b8e3a80-5ca6-11eb-8f18-aaa68784b79e.png">


## Setting up the React app itself
Just a reminder of what a "conventional" React app structure:

```sh
- README.md	
- package.json
- build # if built!	
- public
  - index.html
  ...		
- src
  - App.js
  ...
- yarn.lock
```
The main issue of just marching forward with above setup whilst the repo is under a path rather than just a subdomain of `github.io` will be seeing a blank page. So in our case `layik.github.com/eAtlas` or generally `USER.github.io/REPO` is that React (webpack specifically) and React-Router will need a couple of setups to get it working. What happens if we deploy the app as above is the relative paths will be looking at `layik.github.io` and the `css` and `js` minified files will not be served. 

Therefore:

* Need to tell React that the homepage is the right URL. So in this case, rather than configuring webpack we just add 

```js
{
  ...
  "homepage": "https://layik.github.io/eAtlas"

}
```

* And make sure React-Router pulls the right functions (or component), we also need to provide [`basename`](https://reactrouter.com/web/api/BrowserRouter/basename-string) prop to our BrowerRouter component wherever it is used in the app:

```js
//...
class App extends Component {
  render() {
    return (
      <BrowserRouter 
      basename={process.env.PUBLIC_URL}>
//..
```
[Somewhere](https://create-react-app.dev/docs/deployment/) between `react-scripts` and `webpack` itself the `homepage` is translated to the `env` var of `PUBLIC_URL`. 

## Adding data
So far, we have deployed a web application on GitHub's servers and our backend in TGVE at least, as stated above, ignored. So where do we get data from given CORS? If you can whitelist the newly created GH pages on your own server, then adding it to your own source code is enough. In the case of eAtlas (TGVE) this needs more documentation to be adapted but as it stands it can take a URL from environmental variables per React apps as the default URL. 

However, due to access control you might find yourself limited to what you can pull in. One option here is, of course, to include the data in the repo or pull the data during build which I did not like either. Currently, like Uber Engineers, I decided to pull data from a "data repository" and add it to the eAtlas, for example Vancouver real estate data.

<img src="https://user-images.githubusercontent.com/408568/105985444-5eb03f80-6093-11eb-90f5-bb4f3d3e9090.png" width="100%">

## Adding social meta tags
This is the easy but nowdays crucial part. So far, with setting up a repo like `npx create-react-app my-app` we do not get the tags that are needed. Feel free to find your own list of what tags you like to add but these are the ones I added to the `public/index.html` file of a standard React app.

```html
<!-- other -->
  <meta property="og:title" content="TGVE - eAtlas">
  <meta property="og:description" content="Turing Geovisualization Engine (on GH pages).">
  <meta property="og:image" content="https://user-images.githubusercontent.com/408568/76419738-c46edc80-6398-11ea-8bbe-496394f90adc.png">
  <meta property="og:url" content="https://layik.github.io/eAtlas">
  <meta property="og:site_name" content="TGVE - eAtlas">
  <meta name="twitter:image:alt" content="A simpe dataset on TGVE">
  <meta name="twitter:card" content="summary_large_image">
  <!-- <meta name="twitter:site" content="@sitehandle"> -->
  <meta name="twitter:creator" content="@layik">
  <meta name="twitter:title" content="Turing Geovisualization Engine">
  <meta name="twitter:description" content="Turing Geovisualization Engine (on GH pages).">
  <meta name="twitter:image" content="https://user-images.githubusercontent.com/408568/76419738-c46edc80-6398-11ea-8bbe-496394f90adc.png">
```
If we redeploy with these tags, we can then use Facebook's crawler or Twitter's [validator](https://cards-dev.twitter.com/validator) to check and should see something like this:
<img width="100%" style="border: 1px grey dashed" alt="Screenshot of Twitter validator" src="https://user-images.githubusercontent.com/408568/105491100-b0c91d80-5cad-11eb-89b8-da86f12e3f0b.png">


## Conclusion
We can with some effort deploy a React app on GitHub pages (or our own server space) using the `build` output. The advantage of GitHub pages is the 