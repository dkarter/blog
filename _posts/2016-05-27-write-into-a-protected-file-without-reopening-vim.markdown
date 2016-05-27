---
layout: post
title: "Write into a protected file without reopening Vim"
date: "2016-05-27 14:34:46 -0500"
---

If you ever worked on an SSH session on a remote machine and tried to edit a file that requires super user permissions you might be familiar with the following scenario:

1. You open the file in vim (e.g. nginx.conf)
2. You make a few changes, potentially many
3. You are happy with the result and run the write command `:w`
4. You get an error saying that the file is readonly or that you don't have permissions to write to it
5. Now you are scrambling to copy all your changes and save them somewhere else
6. Then you re-open the file with `sudo vim ...` paste your changes and save. Hopefully everything went smoothly and you did not lose your changes.

**No more!** Next time this happens try this:

```viml
:w !sudo tee %
```

I hope it saves you some frustration.

