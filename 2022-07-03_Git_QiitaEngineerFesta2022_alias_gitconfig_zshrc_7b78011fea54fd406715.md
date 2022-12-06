<!--
title:   僕が使ってる git コマンドのエイリアスを紹介
tags:    Git,QiitaEngineerFesta2022,alias,gitconfig,zshrc
id:      7b78011fea54fd406715
private: false
-->
## はじめに

@ohakutsu が alias の記事を書いていたので私も利用している alias を書いてみました

https://qiita.com/ohakutsu/items/c6abc4588c9d75e54230

## 設定してる Git aliase

`.gitconfig` や `.zshrc` は dotfiles レポジトリに保存しています

https://github.com/mziyut/dotfiles

その中で Git に関連するもの ( かつ よく使うもの ) だけ記載します

### Zsh

```zsh
alias g='git'
```

### Git

```sh
[alias]
	ad = add
	ci = commit
	co = checkout
	cop = !"git branch --all | tr -d '* ' | grep -v -e '->' | peco | sed -e 's+remotes/[^/]*/++g' | xargs git checkout"
	d1 = diff HEAD~
	d2 = diff HEAD~~
	d3 = diff HEAD~~~
	d4 = diff HEAD~~~~
	d5 = diff HEAD~~~~~
	dc = diff --cached
	delete-merged-branches = !git branch --merged | grep -vE '^\\*|master$|main$|develop$' | xargs -I % git branch -d %
	dmas = diff master
	dmai = diff main
	ds = diff --staged
	dw = diff --color-words
	fe = fetch
	ignore = "!gi() { curl -L -s https://www.gitignore.io/api/$@ ;}; gi"
	pl = pull
	st = status
```

Ref: https://github.com/mziyut/dotfiles/blob/master/.gitconfig


## Reference

- https://github.com/mziyut/dotfiles
- https://qiita.com/ohakutsu/items/c6abc4588c9d75e54230