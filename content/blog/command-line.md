+++
title = "Command-line utilities"
date = 2024-02-20
+++

Here is a curated list of tools that I cannot live without, after many iterations (last update: September, 16th 2024). Working on Linux and Windows, I try to use cross-platforms tools.
See also [awesome cli apps list on github](https://github.com/agarrharr/awesome-cli-apps?tab=readme-ov-file). 

- my *Shell* is [nu shell](https://www.nushell.sh/). [fish](https://fishshell.com/docs/current/tutorial.html) is nice but if you want to lose POSIX compatibility, you should go all the way and embrace a new workflow. It considers all data as table, allowing for powerful manipulation. It also
- *Move around quickly* with [zoxide](https://github.com/ajeetdsouza/zoxide). It allows you to jump quickly to a directory with `z boo` (instead of `cd /home/user/misc/books`). A must have
- *Manage tasks* with [taskwarrior](https://taskwarrior.org/) for non Org-mode users. More on that in a future post.
- *Write papers and report* with [typst](https://typst.app/). More on that in a future post.
- `ripgrep` is a modern `grep` but cross-platform and faster
- for the *Terminal*, I use `kitty` that has windows, tabs and image rendering. Alternative are `wezterm` or `alacritty` (with a terminal multiplexer, see below).
- *lazygit* makes working with command-line git easier. It is similar to `magit` in emacs, with less features but good enough for my use case. I prefer `gitui` interface but due to a `libgit2` issue, pushing may fail [https://github.com/extrawurst/gitui/issues/2158](Github).

## Tools that I use occasionally 

- *Zellij* is a rewrite in rust of `tmux`. 2 things that I really like : shortcuts are saner and you can open an editor to copy history (`C-s` then `e`)
- *A easier `find`* with `fd` written in Rust. For example, convert all org files to markdown with 
```bash
       fd -e md -x pandoc {} -o {.}.md`
```
- *An easier `sed`* with `sd`. Example: replace `title:` by `title =` :
```bash
sd 'title:' 'title = '
```
- *Replacement for `ls`* wit `eza` (Rust)
- *Replacement for `cat`* wit `bat` (Rust)

For reference: [other tools written in Rust](https://github.com/sts10/rust-command-line-utilities?tab=readme-ov-file)
