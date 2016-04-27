---
layout: post
title: "Save harddrive space by using git shallow clones"
date: "2016-04-27 00:14:58 -0400"
---

Sometime you clone starter projects, libraries, or plugins to help you get started on a new project or POC.

Perhaps you are using `git clone` to get your project on a remote server.

When you clone a project what you also get is all the previous states of that repository at each commit. This can add up fast and take a good chunk of your hard drive space as well as take long to download and decompress.

To solve this you can clone a shallow copy of the repository by using the `depth` switch on the `git clone` command, and providing it with the value of `1`.

```bash
git clone --depth 1 https://github.com/dkarter/bullets.vim
```


