---
permalink: /ghpages.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

## Deploying a React app on GH pages
GitHub is evolving rapidly and things change faster than many of us can keep up with it. One of the changes is github pages. For instance, it used to restrict developers to a particular folder say `docs` but nowadays we can define where our gh-pages would be including a branch. 

Therefore, there are few tricky bits about gh-pages itself and deploying a React app on it such as Turing Geovisualization Engine (TGVE), or any simple React app which may or may not have a back-end. Needless tosay, for now, though things might change quickly on GH, the backend is out of the question and we cannot have access to processes on GH servers.

So, here are the steps you need to take to deploy your app, or this is how the TGVE has been deployed.

## Setting up CI
One of the recent goodies from GH is GH Actions. This simple `yml` file can get your React app (created using `react-scripts` or `create-react-app`) on a dedicated but orphan branch without messing with your other branches:

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

## Setting up the React repo

## Adding social meta tags

## Conclusion