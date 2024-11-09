+++
title = "Command-line utilities"
date = 2024-02-20
+++

Here is a curated list of tools that I cannot live without, after many iterations (last update is September, 16th 2024). Working on Linux and Windows, I try to use cross-platforms tools. 

## Must-have
- my *shell* is [nushell](https://www.nushell.sh/). [fish](https://fishshell.com/docs/current/tutorial.html) is nice but if you want to lose POSIX compatibility, you should go all the way and embrace a new workflow. It considers all data as table, allowing for powerful manipulation. It makes shell scripting /fun/ by staying is a single language. Also, pandas suppor has been added :)
- *Move around quickly* with [zoxide](https://github.com/ajeetdsouza/zoxide). It allows you to jump quickly to a directory with `z boo` (instead of `cd /home/user/misc/books`). A must have.
- *Manage tasks* with [taskwarrior](https://taskwarrior.org/) for non Org-mode users. More on that in a future post.
- *Write papers and report* with [typst](https://typst.app/). More on that in a future post.
- [ripgrep](https://github.com/BurntSushi/ripgrep) is a modern `grep` but cross-platform and faster.
- for the *terminal*, I use [alacritty](https://github.com/alacritty/alacritty), that is also available on Windows, along with [zellij](https://zellij.dev/) for multiplexing. Alternative are `wezterm` or `kitty` (not available on Windows) that have built-in tabs and windows
- [jj](https://martinvonz.github.io/jj/latest/) has replaced `git` in my
workflow (jj is compatible with git though). But there are nice TUI interfaces
with git worth mentioning : `lazygit` (or `gitui` but [this
issue](https://github.com/extrawurst/gitui/issues/2158) is troublesome).
- search shell history with [atuin](https://atuin.sh/)
- manage dotfiles with [chezmoi](https://www.chezmoi.io/quick-start/)

## For the occasional use
- [zola](https://www.getzola.org/) is a super-fast app for generating the static website (used for this site). There are several, nice-looking theme
- [mdBook](https://rust-lang.github.io/mdBook/) is my go-to way of generating documentation. Theme is very readable and setup is reasonably fast.

## Awesome tools that I no longer use

These may be of interest to other people but these are been superseded in my
workflow. Replacement of `find`, `ls` and `bat` in Rust are really cool but in
practice, `nushell` is enough for my cases:
  - [fd](https://github.com/sharkdp/fd) has an easier syntax than `find` (written in Rust). For example, convert all org files to markdown with ` fd -e md -x pandoc {} -o {.}.md`.)
  - [sd](https://github.com/chmln/sd) also offers a simpler way to replace text  with `sd`. Example: replace `title:` by `title =` : ` sd 'title:' 'title = '`
  - [eza](https://github.com/eza-community/eza) can replace `ls`
  - and [bat](sttps://github.com/sharkdp/bat) is more colorful that `cat`

## Inspiration
Several ideas came from [awesome cli apps list on github](https://github.com/agarrharr/awesome-cli-apps?tab=readme-ov-file). See also [other tools written in Rust](https://github.com/sts10/rust-command-line-utilities?tab=readme-ov-file)
