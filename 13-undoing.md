---
layout: page
title: Version Control with Git
subtitle: Undoing Mistakes
minutes: 25
---
> ## Learning Objectives {.objectives}
>
> *   Restore old versions of files.
> *   Removing mistaken commits.

So
we can save changes to files and see what we've changed&mdash;now how
can we restore older versions of things?
Let's suppose we accidentally overwrite our file:

~~~ {.bash}
$ nano mars.txt
$ cat mars.txt
~~~
~~~ {.output}
We will need to manufacture our own oxygen
~~~

`git status` now tells us that the file has been changed,
but those changes haven't been staged:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   mars.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
~~~

We can put things back the way they were
by using `git checkout`:

~~~ {.bash}
$ git checkout HEAD mars.txt
$ cat mars.txt
~~~
~~~ {.output}
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
But the Mummy will appreciate the lack of humidity
~~~

As you might guess from its name,
`git checkout` checks out (i.e., restores) an old version of a file.
In this case,
we're telling Git that we want to recover the version of the file recorded in `HEAD`,
which is the last saved commit.
If we want to go back even further,
we can use a commit identifier instead:

~~~ {.bash}
$ git checkout f22b25e mars.txt
~~~

It's important to remember that
we must use the commit number that identifies the state of the repository
*before* the change we're trying to undo.
A common mistake is to use the number of
the commit in which we made the change we're trying to get rid of.
In the example below, we want to retrieve the state from before the most
recent commit (`HEAD~1`), which is commit `f22b25e`:

![Git Checkout](fig/git-checkout.svg)

So, to put it all together:

> ## How Git works, in cartoon form {.callout}
> ![http://figshare.com/articles/How_Git_works_a_cartoon/1328266](fig/git_staging.svg)

> ## Simplifying the Common Case {.callout}
>
> If you read the output of `git status` carefully,
> you'll see that it includes this hint:
>
> ~~~ {.bash}
> (use "git checkout -- <file>..." to discard changes in working directory)
> ~~~
>
> As it says,
> `git checkout` without a version identifier restores files to the state saved in `HEAD`.
> The double dash `--` is needed to separate the names of the files being recovered
> from the command itself:
> without it,
> Git would try to use the name of the file as the commit identifier.

The fact that files can be reverted one by one
tends to change the way people organize their work.
If everything is in one large document,
it's hard (but not impossible) to undo changes to the introduction
without also undoing changes made later to the conclusion.
If the introduction and conclusion are stored in separate files,
on the other hand,
moving backward and forward in time becomes much easier.

We might accidentally stage a mistake:

~~~ {.bash}
$ nano mars.txt
$ git add mars.txt
$ cat mars.txt
~~~
~~~ {.output}
There are no bats to use as minions.
~~~

`git status` now tells us we have staged changes waiting to be committed:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   mars.txt
~~~

As `git status` says, we can use `git reset` to unstage files, changing them back into untracked or modified files:

~~~ {.bash}
$ git reset HEAD mars.txt
~~~
~~~ {.output}
Unstaged changes after reset:
M       mars.txt
~~~

This output indicates that `mars.txt` is returning to the modified state.

Let's say the Mummy does not want any record of its involvement. After all, the pilot (a reincarnation of
the Mummy's old rival) may become suspicious. We can change our HEAD to refer to a specific commit by
using `git reset --hard` with the name of a commit:

~~~ {.bash}
$ git reset --hard 34961b1
~~~
~~~
HEAD is now at 34961b1 Add concerns about effects of Mars' moons on Wolfman
~~~

We can see that the log now ends with the specified commit:

~~~ {.bash}
$ git log
~~~
~~~ {.output}
commit 34961b159c27df3b475cfe4415d94a6d1fcd064d
Author: Vlad Dracula <vlad@tran.sylvan.ia>
Date:   Thu Aug 22 10:07:21 2013 -0400

    Add concerns about effects of Mars' moons on Wolfman

commit f22b25e3233b4645dabd0d81e651fe074bd8e73b
Author: Vlad Dracula <vlad@tran.sylvan.ia>
Date:   Thu Aug 22 09:51:46 2013 -0400

    Start notes on Mars as a base
~~~

> ## Reseting nondestructively {.callout}
>
> You may notice that the unstaged changes to `mars.txt` are now gone.
> The `--hard` option is responsible for this: it discards any uncommitted
> changes.
>
> If we didn't use the `--hard` option, all changes since the old commit are now staged.
> This is useful if you want to modify a commit (perhaps to unstage parts of it) and re-commit.


> ## Recovering Older Versions of a File {.challenge}
>
> Jennifer has made changes to the Python script that she has been working on for weeks, and the
> modifications she made this morning "broke" the script and it no longer runs. She has spent
> ~ 1hr trying to fix it, with no luck...
>
> Luckily, she has been keeping track of her project's versions using Git! Which commands below will
> let her recover the last committed version of her Python script called
> `data_cruncher.py`?
>
> 1.
>
>     ~~~
>     $ git checkout HEAD
>     ~~~
> 2.
>
>     ~~~
>     $ git checkout HEAD data_cruncher.py
>     ~~~
> 3.
>
>     ~~~
>     $ git checkout HEAD~1 data_cruncher.py
>     ~~~
> 4.
>
>     ~~~
>     $ git checkout <unique ID of last commit> data_cruncher.py
>     ~~~
> 5. Both 2 & 4
