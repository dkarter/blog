---
layout: post
title: "Bypass aliases with the `command` command"
date: "2016-04-25 23:39:11 -0400"
---

If you are writing a bash script to be used by others and you call external dependencies you might encounter a situation where a user has defined an alias in place of the original command.

For example users of the `hub` command line utillity may have an alias 

```sh
alias git='hub'
``` 

Or something more practically challenging: 

```sh
alias grep='grep  --color=auto --exclude-dir={.bzr,CVS,.git,.hg,.svn}'
``` 

If your command relies on the output of the original program without unexpected modifications you need a way to bypass the aliases and call the command directly.

To bypass aliases prefix the original program call it with the `command` command. For example:

```sh
command git --no-pager diff | command grep '[something]'
```

I like to think of it as the bash equivalent of Vim's `noremap`.


