---
layout: post
title: "Quickly edit and reload tmux configuration"
date: "2016-01-10 19:33:35 -0600"
---
When you are playing around with your tmux config you often need to repeatedly open `.tmux.conf` file and then source it into the current tmux session.

To make things a little faster, add the following to your `.tmux.conf`:

```sh
bind-key r source-file ~/.tmux.conf \; display-message "~/.tmux.conf reloaded"
bind-key M split-window -h "vim ~/.tmux.conf"
```

Now you can quickly open the tmux config in tmux pane using `<prefix>` followed by `shift + M`, edit your configuration, and save when you done. To reload the tmux configuration use `<prefix>` followed by `r`.

** `<prefix>` is `C-z` for hashrocket's dotfile project (dotmatrix), if you are using tmux default configuration it would be `C-b`


