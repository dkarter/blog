---
layout: post
title: "watch for database changes on postgresql"
date: "2016-03-08 21:32:19 -0600"
---


If you are tyring to debug multi-threaded environments or a really fast job
queue you might need to "watch" a table for changes.

In `psql` use the `\watch` command like so:

```
\watch 1 "select * from job_queue";
```

This will run the query every 1 second (you can change the second argument if
you need it slower) and display the result as well as past results.

