---
layout: post
title: "check if ruby gem is installed from bash script"
date: "2015-12-28 16:52:09 -0600"
---

If you are adding customizations to your zshrc, such as adding auto completion for a certain gem, you want to make sure that the gem is installed before performing any action.

The `gem list [gemname] -i` command returns a boolean representing whether a gem is installed or not.

```bash
if `gem list lunchy -i`; then
  echo "Lunchy gem is installed!";
  # do some configurtion here
fi
```

If you wish to be more granular you can check whether a specific version is installed by adding the `--version [version]` flag.


```bash
if `gem list lunchy -i --version 0.10.4`; then
  echo "Lunchy v0.10.4 is installed!";
  # do some configurtion here
fi
```


