---
layout: post
title: "Node 10 Broke Everything"
date: "2018-05-09 19:04:10 -0500"
---

## What Happened
> TL;DR homebrew updated my node version to 10.0.0 which broke things
> particularly with global packages installed through Yarn.

Recently I noticed a bunch of plugins in Vim that rely on npm packages started
displaying weird error messages. (Specifically `Galooshi/vim-import-js`) I also
noticed that any attempt to install node modules globally would ultimately fail
due to native extensions compilation.

```
Error detected while processing function <SNR>226_JobHandler:
line    7:
import-js error: internal/modules/cjs/loader.js:550     throw err;     ^  Error: Cannot find module '/Users/dkar
ter/.config/yarn/global/node_modules/sqlite3/lib/binding/node-v59-darwin-x64/node_sqlite3.node'     at Function.
Module._resolveFilename (internal/modules/cjs/loader.js:548:15)     at Function.Module._load (internal/modules/c
js/loader.js:475:25)     at Module.require (internal/modules/cjs/loader.js:598:17)     at require (internal/modu
les/cjs/helpers.js:11:18)     at Object.<anonymous> (/Users/dkarter/.config/yarn/global/node_modules/sqlite3/lib
/sqlite3.js:4:15)     at Module._compile (internal/modules/cjs/loader.js:654:30)     at Object.Module._extension
s..js (internal/modules/cjs/loader.js:665:10)     at Module.load (internal/modules/cjs/loader.js:566:32)     at
tryModuleLoad (internal/modules/cjs/loader.js:506:12)     at Function.Module._load (internal/modules/cjs/loader.
js:498:3)
```

Wait what!? Node 10.0.0??? Where did that come from?

If you are running mac you may have gotten node as a dependency to one of the
apps you have installed through there. And since
[Homebrew](https://github.com/Homebrew/brew) updates pour in fast you may not
have noticed that on the latest upgrade you got a shiny new version of Node.
Normally I would be happy about that - except the new version caused many
modules to break.

I am also managing Node versions using the wonderful
[asdf](https://github.com/asdf-vm/asdf), an extensible version manager for multiple
languages including Ruby, Node.js, Elixir, Erlang & more.

In my project I had cemented Node version 9.11.1 (`asdf current nodejs 9.11.1`)
in hopes that this would fix the issue, but every time I opened Vim I would
still get the error from import-js.

Yarn global package installation was completely broken and was ignoring my
cemented node version in my project directory. The error mentioned v10.0.0.

I have since noticed similar issues on other machines in my office which
prompted me to write this blog post.


## How to Fix It
First check if you have **Yarn** installed through homebrew

```sh
brew list | grep yarn
```

If you get the word node back, it means it is! Let's remove it:

```sh
brew uninstall yarn
```

Now let's see if you have **Node** installed through homebrew:

```sh
brew list | grep node
```

If you get the word node back, it means it is! Let's remove it:

```sh
brew uninstall --ignore-dependencies --force node
```

Next let's get Node installed with asdf. Instead of installing asdf from
homebrew, which I've seen issues with, I recommend following the directions on
[the asdf Github repo](https://github.com/asdf-vm/asdf). Namely:

Copy-paste the following into command line:

```bash
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.4.3
echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.zshrc
echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.zshrc
source ~/.zshrc
```

Now install the `nodejs` plugin for asdf:

```sh
asdf plugin-add nodejs
```

And install the OpenPGP public keys for verifying downloaded Node binaries:

```sh
bash ~/.asdf/plugins/nodejs/bin/import-release-team-keyring
```

Next you will need to install Node through adsf:

```sh
asdf install nodejs 9.11.1
```

To install other versions see `asdf list-all nodejs`

Set it as the global version:

```sh
asdf global nodejs 9.11.1
```

Next you are going to want to install yarn globally using the version of Node
installed with asdf:

```sh
npm i -g yarn
```

If you have previously configured Yarn's global path to be added to your `$PATH`
env var (see my previous post [Yarn
global](https://til.hashrocket.com/posts/abfcbb7613-yarn-global)) you will need
to ensure that line is now below the asdf setup in your `~/.zshrc`. For more
info check my new post [Using `yarn global` w/ Node through asdf
(mac)](https://til.hashrocket.com/posts/esd7dvzfme-using-yarn-global-w-node-through-asdf-mac)

**This is the key to it all having yarn run with the node version you installed
from asdf instead of a global version determined by homebrew!** `yarn global`
should now work like a charm.

Now you can do the same with version 10 of Node, running it alongside version
9.11 and experimenting with it until you feel like it is stable enough to be
your global version.

## Conclusion
Having thing break can sometimes be a good thing because it can lead you to a
better setup overall. I like having node related programs such as yarn installed
via npm, and I like even more the ability to control my version of Node used
globally and specifically within project directories. ASDF does a great job of
being easy to install and configure.
