---
title: 【Git】Git reset hard vs. soft
description: 
slug: 
date: 2023-10-17 16:23:00.000 +0800
image: 
categories:
    - Frontend
tags:
    - Git
---

`git reset` is a versatile command that resets HEAD to a specified state.

# git reset [\<mode>] [\<commit>]

This form resets the current branch head to \<commit> and <b>possibly</b> updates the index (resetting it to the tree of \<commit>) and the working tree depending on \<mode>. Before the operation, ORIG_HEAD is set to the tip of the current branch. If \<mode> is omitted, defaults to \--mixed. The \<mode> can be one of the following:

## \--soft

Does not touch **the index file** or **the working tree** at all (but resets **the head** to \<commit>, just like all modes do). This leaves all your changed files "Changes to be committed", as git status would put it.

## \--mixed

Resets the index but not the working tree (i.e., the changed files are preserved but not marked for commit) and reports what has not been updated. This is the default action.

If -N is specified, removed paths are marked as intent-to-add (see git-add[1]).

## \--hard

Resets the index and working tree. Any changes to tracked files in the working tree since <commit> are discarded. Any untracked files or directories in the way of writing any tracked files are simply deleted.

# References

1. [Git reset hard vs. soft: What's the difference?](https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/Git-reset-hard-vs-soft-Whats-the-difference)
2. [Git - git-reset Documentation](https://git-scm.com/docs/git-reset)