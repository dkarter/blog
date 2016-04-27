---
layout: post
title: "Find (and kill) all processes listening on a port"
date: "2016-04-26 23:56:37 -0400"
---

To search for processes that listen on a specific port use the `lsof` or "List Open Files". The `-n` argument makes the command run faster by preventing it from doing a ip to hostname conversion. Use grep to show only lines containing the word LISTEN.

```bash
lsof -n | grep LISTEN
```

To filter for a specific port use the following:

```bash
lsof -n -i4TCP:[PORT] | grep LISTEN
```

To kill all processes listening on a specific port use:

```bash
lsof -n -i4TCP:[PORT] | grep LISTEN | awk '{ print $2 }' | xargs kill
```

The `awk` command returns only the second column (PID), and the `xargs` executes `kill` on each line returned.

