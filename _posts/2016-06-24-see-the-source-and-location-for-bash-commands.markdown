---
layout: post
title: "See the source and location for bash commands"
date: "2016-06-24 19:12:40 -0500"
---

The `oh-my-zsh` package comes with a lot of useful plugins. Often those plugins add bash functions to the global scope.

If you want to see where the function is defined you can use the `type` command.

For example the `new_gh`/`exist_gh` command which creates a remote repo for a folder:

```bash
$ type exist_gh
exist_gh is a shell function from /Users/dkarter/.oh-my-zsh/plugins/github/github.plugin.zsh
```

To see the function source use the `declare` command:

```bash
$ declare -f new_gh
new_gh () {
        cd "$1"
        ghuser=$( git config github.user )
        git init
        print '.*'"\n"'*~' &gt;&gt; .gitignore
        git add ^.*
        git commit -m 'Initial commit.'
        git remote add origin git@github.com:${ghuser}/${repo}.git
        git push -u origin master
}
```


Originally published by me on November 30, 2015 at [Hashrocket
TIL](https://til.hashrocket.com/posts/15daca5f17-see-the-source-and-location-for-bash-commands).

Hashrocket is an [expert team of developers, designers and consultants](https://hashrocket.com) specializing in [Ruby on Rails, React, Ember, Elixir and mobile](https://hashrocket.com/what-we-do).

