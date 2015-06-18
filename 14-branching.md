---
layout: page
title: Version Control with Git
subtitle: Side-by-Side Development
minutes: 25
---
> ## Learning Objectives {.objectives}
>
> *   Create and checkout branches.
> *   Create commits that are the merger of multiple branches.

Sometimes it is useful to work on a group commits together and keep them separate from your main
history. For example, Wolfman might work on changing the transfer orbit calculations to take into
account the availability of solar sail technology to create lighter spacecraft. This might take
several days, and Wolfman might want to work on this while everyone is still working on the old code.

Branches are also useful when you have multiple independent aspects of a project to work on. For example,
while updating your LabView programming to take more detailed measurements, you might realize the same
program needs to be used to generate data for a conference in a few days. You need a couple changes for
the conference, but do not want to involve (or use) the changes for more detailed measurements. Putting
the latest work in a separate branch allows simple multitasking without affecting each project. After the
programming for the more detailed measurements is done, the fixes in one branch can be merged into the other.

By convention, Git projects have a `master` branch and large additions/changes happen by merging branches.
This has the advantage that the non-`master` branches can be reordered, rewritten, (or otherwise have their
history changed); and `master` will contain a nicely organized commit history:

> ## How Git branches are used, by convention {.callout}
> ![http://img.hades.name/blog-media/git/git-history.png](fig/git-branch-history.png)

So let's take a look at how to tell what branch we are developing on and what branches exist. Many commands, including
`git status`, has indicated the current branch:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
On branch master
nothing to commit, working directory clean
~~~

We can also view branches by using the `--decorate` option to `git log`:

~~~ {.bash}
$ git log --oneline --decorate
~~~
~~~ {.output}
cab71b9 (HEAD, master) Add ignore file
26be060 Add conerns about effects of Mars' moons on Wolfman
3dd9a07 Start notes on Mars as a base
~~~

To create a new branch, use `git branch`:

~~~ {.bash}
$ git branch major-changes
~~~

This creates a branch named `major-changes` that is located at the current head. This can be seen in the log:

~~~ {.bash}
$ git log --oneline --decorate
~~~
~~~ {.output}
cab71b9 (HEAD, master, major-changes) Add ignore file
26be060 Add conerns about effects of Mars' moons on Wolfman
3dd9a07 Start notes on Mars as a base
~~~

We can also see that we haven't changed the current branch by using `git status`:

~~~ {.output}
On branch master
nothing to commit, working directory clean
~~~

Switching branches is another use of the `git checkout` command:

~~~ {.bash}
git checkout major-changes
~~~
~~~ {.output}
Switched to branch 'major-changes'
~~~

> ## Creating and Using a New Branch {.callout}
>
> Instead of using `git branch` to create a branch, and `git checkout` to switch to it;
> you can just use `git checkout -b` to checkout a new branch.

Perhaps we would like to change all the text files to be numbered. Edit `mars.txt` to
number the lines, then commit the change:

~~~ {.bash}
$ git commit -a -m "Number each note about planet"
~~~
~~~ {.output}
[major-changes 992de4b] Number each note about planet
 1 files changed, 3 insertions(+), 3 deletions(-)
~~~

We can see that this commit did not affect the `master` branch by looking at the decorated
log again:

~~~ {.bash}
$ git log --oneline --decorate
~~~
~~~ {.output}
992de4b (HEAD, major-changes) Number each note about planet
cab71b9 (master) Add ignore file
26be060 Add conerns about effects of Mars' moons on Wolfman
3dd9a07 Start notes on Mars as a base
~~~

We can even go back to the `master` branch and see the log does not contain the newest changes:

~~~ {.bash}
$ git checkout master
$ git log --oneline --decorate
~~~
~~~ {.output}
cab71b9 (HEAD, master) Add ignore file
26be060 Add conerns about effects of Mars' moons on Wolfman
3dd9a07 Start notes on Mars as a base
~~~

The commits in other branches can be viewed with the `--all` option:

~~~ {.bash}
$ git log --oneline --decorate --all
~~~
~~~ {.output}
992de4b (major-changes) Number each note about planet
cab71b9 (HEAD, master) Add ignore file
26be060 Add conerns about effects of Mars' moons on Wolfman
3dd9a07 Start notes on Mars as a base
~~~

A central tenant of Git is the ease at which branches can be merged &mdash;some open source
work on all their features in separate branches before merging them to the `master` branch.

To incorporate the changes in the `major-changes` branch, use `git merge`:

~~~ {.bash}
$ git merge major-changes
~~~
~~~ {.output}
Updating cab71b9..992de4b
Fast-forward
 mars.txt    | 6 +++---
 1 files changed, 3 insertions(+), 3 deletions(-)
~~~

We can verify the changes have been merged by looking at the log again:

~~~ {.bash}
$ git log --oneline --decorate --all
~~~
~~~ {.output}
992de4b (HEAD, master, major-changes) Number each note about planet
cab71b9 Add ignore file
26be060 Add conerns about effects of Mars' moons on Wolfman
3dd9a07 Start notes on Mars as a base
~~~

And we can now safely remove the old branch:

~~~ {.bash}
$ git branch -d major-changes
~~~
~~~ {.output}
Deleted branch major-changes (was 992de4b).
~~~
