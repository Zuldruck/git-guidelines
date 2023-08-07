# git-guidelines

## Introduction

The main goal of this workflow, is to have, in the end a `main` / `master` branch as clean as possible. In this example, the main branches will be `main` and `dev`. The basic concepts of [git branching and merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging) needs to be understood.

By clean we mean a linear git history in which we can quickly find what has been done, when it has been done and what commits belong to what branch and when that branch is merged. The objective is to avoid having a train station when doing a `git log --graph`.

In order to achieve this we will use as main a tool, the `git rebase` [command](https://git-scm.com/docs/git-rebase).

## Setup

In order to start working with this workflow we need to setup properly our git environment :

- First, we need to configure the rebase as default behaviour when pulling a branch (compared to merge by default) `git config pull.rebase true`.
- Then, we always want to have merge commits to know when the merge occurs and what commits are concerned `git config merge.ff no`, by doing this we disable the automatic fast-forward.

## Workflow

Now let's talk about the workflow we will use.
These are the steps to follow when starting a new feature : 

- Create a new branch based on `main`, let's call it `feature`
- Do your changes and commit them by using this simple rule : 1 individual change is 1 commit. When you want to fix something you already commited, do not create a new commit, but use rebase to squash your chanegs to the old commit.
- When all the commits are done and the feature is finished, you can rebase your banch on `main` in case changes were made on the `main` branch : from your`feature` branch, `git rebase main`. Be careful, if you are working from an intermediate branch you don't want to loose the other merge commits that were made on the `feature` branch. To avoid it you can `git rebase main --rebase-merges`.
- In case you are working with someone else on that branch (that should be avoided), `git pull feature`.
- Finally, push and create your Pull Request.

Then, loop this process until the Pull Request is approved : 

- Create a fixup commit (see the `--fixup` option of [git commit](https://git-scm.com/docs/git-commit)) for each individual change that were made which need a modification (due to the review of the pull request).
- Push and wait for review approval.

Then, loop this process until the QA approves your work :

- Create a fixup commit for each individual change that were made which need a modification.
- Push your branch.
- Merge your branch on the `dev` branch and push it (and trigger the deployment process if there is one).

Once the pull request and the QA process is done, you can :

- Rebase on `main` by applying your fixups on the original commits, to do so : `git rebase main -i --autosquash`.
- When the rebase is finished, merge your branch on the `main` branch (and trigger the deployment process if there is one).
- Finally, to keep the `dev` branch up to date and to avoid having unnecessary changes during pull requests, don't hesistate to merge `main` on `dev`.

## Tips

Using the rebase can possibly involve to deal with the same conflicts several times on long living branches. To avoid having to deal with it you can use [git rerere](https://git-scm.com/docs/git-rerere) which will record the resolution for each conflicts and use that resolution each time it encounters the same conflict. It can also be configured to always be used when encountering conflicts `git config rerere.enabled true`.
