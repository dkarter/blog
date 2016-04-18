---
layout: post
title: "Repeatable operations on search matches with gn"
date: "2016-04-17 22:16:03 -0500"
---

When you find yourself having to operate on a certain string, you may first search for it:

```
:/ReentrantLock
```

To change it you will usually use a repeatable operation such as `ciw` followed by whatever you are changing it to. 

Now to repeat that change you will first jump to the next search result using `n` then press the `.` operator to repeat the change. But if you know that you are going to do that there's an easier way:

Instead of `ciw` use `cgn`. Then just press the `.` and Vim will jump to the next match and change it for you.

According to Vim's help:

> `gn` - Searches forward for the last used search pattern, like with `n`, and starts Visual mode to select the match.

for more info:

```
:h gn
or 
:h gN (for searching backwards)
```

**Note:** this also works with `*` which searches for the word currently under the cursor.


