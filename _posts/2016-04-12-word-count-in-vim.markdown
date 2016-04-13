---
layout: post
title: "Word Count in Vim"
date: "2016-04-12 23:52:17 -0500"
---

Vim has a built in shortcut for counting words. To see the word count for the whole buffer type:

```
g<CTRL-g>
```

You will see something like:

```
Col 11 of 54; Line 12 of 24; Word 39 of 68; Byte 360 of 727
```

Meaning we are currently at word 39 and the whole buffer contains 68 words.

To see the word count for a selection, first select and type the same shortcut `g<CTRL-g>` the result should look something like this: 

```
Selected 7 of 24 Lines; 23 of 68 Words; 234 of 727 Bytes
```

in this case I have selected 23 words.

