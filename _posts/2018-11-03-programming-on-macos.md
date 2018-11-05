---
layout: post
title: Programming on MacOS in 2018
category: blog
tags: [programming, macos]
---

Many of my coworkers are about to switch to macs for their development machines, so this seems like a good opportunity to make recommendations for MacOS software that I find useful.

Many of these depend on the xcode command line tools. I think these consist of some compilers maybe? Anyway, install them first with `xcode-select --install`.

### Homebrew

The missing package manager for mac. This is essential for programmers. Many software you'll want is easiest to install using homebrew. Get it at [brew.sh](https://brew.sh/).

### Spectacle

Super fast keyboard-driven window manager. Free yourself from the tyranny of manually resizing windows. This is the app I miss the most when I'm using someone else's computer. Get it at [spectacleapp.com](https://www.spectacleapp.com/).

### oh-my-zsh

A better terminal experience. I have a hard time describing precisely what's better about zsh over bash. It's almost the same, but just works... better. Tab completion in particular is more intelligent.

I use [oh my zsh](https://ohmyz.sh/) with pretty much all defaults, and it gives me

- Autocomplete for my git branch names
- Git info on my prompt: whether there are uncommitted changes, and the name of my current branch.
- A symbol that is green if the last command I ran was successful, red if it failed. This saves me from nervously checking `echo $?` to see exit codes.

I also installed [fzf](https://remysharp.com/2018/08/23/cli-improved#fzf--ctrlr) as a replacement for the built-in ctrl+r behavior (searching history of recently used commands). Install with `brew install fzf && $(brew --prefix)/opt/fzf/install`.

### Alfred

This is one of the only paid software on this list. There's a free version, but it doesn't do any of the good stuff. I use alfred primarily for

- Clipboard history. ⇧⌘v (shift + command + v) opens a dialog to let me choose from any of my recently copied items.
- [Clipbox](https://github.com/bgschiller/alfred-clipbox). Press a shortcut to quickly take a screenshot (⇧⌘x). It will be uploaded to trello, and the direct url placed on your clipboard. Also works for files and text (⇧⌘c), and screen recordings (⇧⌘g).
- [Password management](https://brianschiller.com/blog/2016/08/31/gnu-pass-alfred). Really GNU Pass is the brains behind my password management setup, but Alfred provides the UI.

There's a ton of other extensions, but I haven't taken the time to learn any of them.

## Depending on your work

These may not be necessary, depending on what you're working on.

### Python3

There are a ton of different ways to install python on a mac, as captured in [this xkcd](https://xkcd.com/1987/). I use

- homebrewed python3 (`brew install python`).
- pyenv (`brew install pyenv`), if I need a different version of python for a specific project.
- pipenv (`brew install pipenv`), to keep different projects dependencies separate from one another (eg, you might need to use Flask v0.12 for one project, and v1.0.2 for another).

To set up a pipenv using a specific version of python:

```bash
pyenv local 3.6.6 # or whatever version you need for this project
pipenv --python $(pyenv which python)
```

#### TypeError: 'module' object is not callable

Lately, when running `pipenv install` on a fresh pipenv, I get the following error:

```
➜  WebScraping git:(web-scraping) ✗ pipenv install
Pipfile.lock (2ad753) out of date, updating to (492d77)...
Locking [dev-packages] dependencies...
tils.py", line 402, in resolve_deps
    req_dir=req_dir
  File "/Library/Python/2.7/site-packages/pipenv/utils.py", line 250, in actually_resolve_deps
    req = Requirement.from_line(dep)
  File "/Library/Python/2.7/site-packages/pipenv/vendor/requirementslib/models/requirements.py", line 704, in from_line
    line, extras = _strip_extras(line)
TypeError: 'module' object is not callable
```

This bug is tracked at [pypa/pipenv#2924](https://github.com/pypa/pipenv/issues/2924). Luckily, there's a really easy fix. Just run:

```
pipenv run pip install pip==18.0
# now pipenv install will work fine
```

### Postico

The best darn database GUI I've ever used. Only for Postgres, though.

### Node

Similar to python, you'll want to have a way to switch between different versions of node for different projects. `nvm` is a project that lets you do this.

Run the install script from [creationix/nvm](https://github.com/creationix/nvm#install-script). As part of the script, a new line is written to your .zshrc, so you'll have to open a new terminal in order to use nvm.

Run `nvm install 10.11.0` (or whatever version you want). You can see available versions with `nvm ls-remote`. It's useful to have a default version set: `nvm alias default 10.11.0`