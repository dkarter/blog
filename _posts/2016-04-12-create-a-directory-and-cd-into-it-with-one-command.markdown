---
layout: post
title: "Create a directory and cd into it with one command"
date: "2016-04-12 23:49:46 -0500"
---

I don't know about you but I tend to create directories from terminal often, and when I do, `cd`ing into them is the first thing I'll do after creating the directory.

This is what it usually looks like

```bash
mkdir -p foo/bar/baz
cd foo/bar/baz
```

This can get quite verbose. You may have also tried this pro tip:

```bash
mkdir -p foo/bar/baz
cd !$ # press <TAB> to expand !$ to the last argument of the previous command
```

But I think this can be even simpler and easier. Add the following to your .zshrc or .bashrc:

```bash
mkcd() {
  mkdir -p $1 && cd $1
}
```

Now source .zshrc/.bashrc and simply run:

```bash
mkcd foo/bar/baz
```

BOOM 💥
