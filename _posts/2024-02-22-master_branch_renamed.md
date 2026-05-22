---
layout: post
title: AtomVM `master` branch renamed
excerpt_separator: <!--more-->
---

Earlier this month the name of the [AtomVM](https://github.com/atomvm/AtomVM) `master` branch was renamed to `main`.  If you have previously made a local clone of the repository, and would like to keep up to date with changes to the `main` branch, there are a few `git` commands that will rename your local copy and update the git configuration to track the `main` AtomVM branch on GitHub.

From inside your local clone of [AtomVM](https://github.com/atomvm/AtomVM):

    shell$ git branch -m master main
    shell$ git fetch origin
    shell$ git branch -u origin/main main
    shell$ git remote set-head origin -a

At this point you now have a local clone that will stay up to date with changes in the upstream AtomVM `main` branch.

We have also recently created the `release-0.6` branch, which should be used if you wish to contribute any bug fixes.  If you do find a bug, please open an [issue on GitHub](https://github.com/atomvm/AtomVM/issues).

If you encounter a feature you think we are lacking, please [open an issue](https://github.com/atomvm/AtomVM/issues) for that too.  Any contributions of new features should be made on the `main` branch.

The AtomVM team
