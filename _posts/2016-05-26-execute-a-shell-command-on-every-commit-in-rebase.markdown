---
layout: post
title: "Execute a shell command on every commit in rebase"
date: "2016-05-26 10:49:24 -0500"
---

Git rebase allows you to execute a command on any commit in a rebase:

```sh
git rebase -i -exec "git reset-authors"
```

The result will look something like this:

```git
pick xxxyyy Do stuff to things
exec git reset-authors
pick yyyzzz Do things to stuff
exec git reset-authors
```

It is very useful for resetting authors on multiple commits in case you forgot to set the current pair.

P.S. If you are curious about what `git reset-authors` does check out our `.gitconfig` in Hashrocket's dotfiles:

```ini
[alias]
reset-authors = commit --amend --reset-author -CHEAD
```

https://github.com/hashrocket/dotmatrix/blob/master/.gitconfig#L18


