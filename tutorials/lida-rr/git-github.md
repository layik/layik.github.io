Git/GitHub workshop
================
L Hama
2020-10-04

  - [Introduction](#introduction)
  - [Hands on](#hands-on)
  - [GitHub](#github)
  - [Branch](#branch)
  - [Awesomeness](#awesomeness)
  - [References](#references)

Workshop date: 5th October 2020 Estimate time: One hour Location: MS
Teams

### Introduction

Official [docs](https://git-scm.com):

> Git is a [free and open
> source](https://git-scm.com/about/free-and-open-source) distributed
> version control system designed to handle everything from small to
> very large projects with speed and efficiency.

You can [find](https://git-scm.com/book/en/v2) the “Pro Git” book from
Scott Chacon and Ben Straub free to read.

But this part is important:

> Git thinks of its data more like a series of `snapshots` of a
> `miniature filesystem`. With Git, every time you `commit`, or save the
> state of your project, Git basically takes a picture of what all your
> files look like at that moment and stores a reference to that
> `snapshot`. To be efficient, if files have not changed, Git doesn’t
> store the file again, just a link to the previous identical file it
> has already stored. Git thinks about its data more like a `stream of
> snapshots`.

Visualized:
<img src="https://git-scm.com/book/en/v2/images/snapshots.png" alt="git visualized" max-width="100%">
Image from (Chacon and Straub 2014)

### Hands on

Let us run this session with each of you doing at least one or more
commands and the rest of us will follow/lead/watch.

I just copied the titles of the section two of the book here but we will
do it our way:

2.  Git Basics 2.1 Getting a Git Repository

Creating on our machine

``` sh
mkdir repo # anywhere on your machine
git init # 
git status
```

Or show us how you can do this using GitHub desktop? I found this
[link](https://docs.github.com/en/free-pro-team@latest/desktop/installing-and-configuring-github-desktop/creating-your-first-repository-using-github-desktop)
but never tried the application.

Cloning from a remote?

``` sh
git clone https://github.com/layik/eAtlas
```

2.2 Recording Changes to the Repository

``` sh
# write some R code
echo "print('Hello world')" >> hello-world.R
git status
git add *.R
git status
git commit -m "my first file added"
git status
```

2.3 Viewing the Commit History

``` sh
git log
git log --oneline
```

2.4 Undoing Things

``` sh
# edit the file hello-world.R
git status
# undo
git checkout hello-world.R
git status
```

2.5 Working with Remotes

``` sh
git remote -v 
# none?
# time to create our first github repository!
# www.github.com
# new private repo or if brave enough make it public
# come back and bring the instructions shown on github
# git remote add ...
```

Creating a repo on
[github](https://guides.github.com/activities/hello-world/)?
<img src="https://guides.github.com/activities/hello-world/create-new-repo.png" alt="create repo on github" width="100%">

Lets be brave and send the current commits to the remote.

``` sh
# try 
git push # error message?
```

2.6 ~~Tagging~~

2.7 ~~Git Aliases~~

2.8 Summary

  - created our first repo on our machine
  - added file(s) to it
  - can check history of the “commits”
  - can add remote repositories

### GitHub

There is a great interactive GitHub [guides](https://guides.github.com)
pages.

#### README

The `index` file of GitHub. Just open a repository and compare what you
see on the landing with the file `README.md`

#### GH pages

<https://USER.github.io/repo>

A repo with `USER.github.io` will translate to: <https://USER.github.io>
for example `layik.github.io` actually points to:
<https://github.com/layik/layik.github.io>

Worth mentioning R packages: \* `packagedown` \* `bookdown` \*
`devtools::install_github` \* `covr`?

#### DOI

Checkout this short
[tutorial](https://guides.github.com/activities/citable-code/) to get
one on the repo.

### Branch

3.  Git Branching

3.1 Branches in a Nutshell

> A branch in Git is simply a lightweight movable pointer to one of
> these commits. The default branch name in Git is master.

Read the
[rest](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)
of the section in the book. 3.2 Basic Branching and Merging

``` sh
# branch or no branch, you can always branch
git branch <name>
git checkout <name>
# combine those two 
git checkout -b <name>
git branch 
git status
```

Lets edit `hello-world.R`

``` sh
# this will append the comment to the file
echo "# some R comment" >> hello-world.R
# or just
vim hello-world.R 
# and add some changes
git status
# a for all staged
# m for message required for commits
git commit -am "added oneline comment to hello-world.R"
```

Or something or change somethign on your branch:

``` sh
echo "File to merge" >> fix.txt
git status
git add fix.txt
git status
git commit -am "add fix.txt file to branch <name>"
```

Go back to master just to see one or both of those changes

``` sh
git status
git merge <name>
# voila!
```

Create a branch on
[GitHub](https://guides.github.com/activities/hello-world/)? (not
recommended :))
<img src="https://guides.github.com/activities/hello-world/readme-edits.gif" alt="gif create branch on githu" width="50%">

3.3 Branch Management A whole section from the book which is great.
Picks for this one hour tutorial:

``` sh
git branch
# notice the asterisk
git branch -v 
# productive!
```

When working with github and you want to create your first PR (pull
request):

``` sh
git push origin <name>
# just created a branch called <name> on remote go check.
```

Delete locally and remotely?

``` sh
git branch -D <name>
# did that work?
git branch
# now this beauty
git push origin --delete <name>
```

3.4 ~~Branching Workflows~~ You will want to read
[this](https://git-scm.com/book/en/v2/Git-Branching-Branching-Workflows)
in future and no doubt will probably have your own way of doing things.

3.5 Remote Branches

In this section just want to highlight “branch tracking”: Your colleague
just created a branch and you want to edit something and send it back to
them.

``` sh
git checkout --track origin/<name>
```

3.6 ~~Rebasing~~

3.7 Summary

  - Got an idea about the character called “branch” in “Trolls”?
  - Branching is the essence of collaborating and managing source code.
  - The method of collaborating is via remote location(s)
  - All this enables Continous Integration or Continous Delivery
  - Automation is the ultimate goal\!

### Awesomeness

  - [GitHub Official](https://guides.github.com)

### References

<div id="refs" class="references hanging-indent">

<div id="ref-progit">

Chacon, Scott, and Ben Straub. 2014. *Pro Git*. Springer Nature.

</div>

</div>