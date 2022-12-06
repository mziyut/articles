<!--
title:   macOS 上に Rust の実行環境を用意する
tags:    AdventCalendar2022,Rust,fish-shell,homebrew,macOS
id:      3127eb3d975346b1d795
private: false
-->
## はじめに

最近 Rust を学んでおり macOS 上に Rust の実行環境を用意しました
Rust の設定方法としていくつか方法があったため、この方法が良かったとメモを残しておきます

## 今回のGoal

- `rustc`, `rustup`, `cargo` 等の rust に関連するコマンドが実行出来る状態

## セットアップ環境

```zsh
$ sw_vers
ProductName:            macOS
ProductVersion:         13.0.1
BuildVersion:           22A400
```

shell は `fish-shell` を利用し、 Homebrew がセットアップされている前提です

https://brew.sh/

## 実行環境を用意する

### 事前準備

もし、 Homebrew 等で `rust` を install していたら uninstall しておきましょう

```zsh
brew uninstall rust
Uninstalling /usr/local/Cellar/rust/1.64.0... (35,008 files, 869.7MB)
```

`rust` が install されたままセットアップを続けると以下のようなエラーが出る場合があります

```zsh
info: downloading installer
warning: it looks like you have an existing installation of Rust at:
warning: /usr/local/bin
warning: rustup should not be installed alongside Rust. Please uninstall your existing Rust first.
warning: Otherwise you may have confusion unless you are careful with your PATH
warning: If you are sure that you want both rustup and your already installed Rust
warning: then please reply `y' or `yes' or set RUSTUP_INIT_SKIP_PATH_CHECK to yes
warning: or pass `-y' to ignore all ignorable checks.
error: cannot install while Rust is installed

Continue? (y/N)

error: cannot install while Rust is installed
```

### "rustup" を利用し環境を作成する

`rustup` という shell script を取得しましょう
取得先としては、公式サイトに記載されている方法や、"Homebrew" を用いる方法など様々あります

https://www.rust-lang.org/tools/install

https://formulae.brew.sh/formula/rustup-init#default

今回は、複数マシンで同じ環境となるように Brewfile を dotfiles レポジトリで管理している都合上 "Homwbrew" 経由で "rust" を取得しセットアップしていきます

```zsh
brew install rustup-init
```

Homwbrew 経由で `rustup-init` の install が完了したら `rustup-init` を実行し、 `cargo`, `rustc` 等を設定しましょう
途中 install 方法の選択を求められますが "1) Proceed with installation (default)" を選択しましょう ( CLI 上に 1 と入力すれば OK )

```zsh
$ rustup-init
Welcome to Rust!

This will download and install the official compiler for the Rust
programming language, and its package manager, Cargo.

Rustup metadata and toolchains will be installed into the Rustup
home directory, located at:

  /Users/mziyut/.rustup

This can be modified with the RUSTUP_HOME environment variable.

The Cargo home directory is located at:

  /Users/mziyut/.cargo

This can be modified with the CARGO_HOME environment variable.

The cargo, rustc, rustup and other commands will be added to
Cargo's bin directory, located at:

  /Users/mziyut/.cargo/bin

This path will then be added to your PATH environment variable by
modifying the profile files located at:

  /Users/mziyut/.profile
  /Users/mziyut/.bashrc
  /Users/mziyut/.zshenv

You can uninstall at any time with rustup self uninstall and
these changes will be reverted.

Current installation options:


   default host triple: x86_64-apple-darwin
     default toolchain: stable (default)
               profile: default
  modify PATH variable: yes

1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
>1

info: profile set to 'default'
info: default host triple is x86_64-apple-darwin
info: syncing channel updates for 'stable-x86_64-apple-darwin'
info: latest update on 2022-11-03, rust version 1.65.0 (897e37553 2022-11-02)
info: downloading component 'cargo'
info: downloading component 'clippy'
info: downloading component 'rust-docs'
info: downloading component 'rust-std'
info: downloading component 'rustc'
info: downloading component 'rustfmt'
info: installing component 'cargo'
info: installing component 'clippy'
info: installing component 'rust-docs'
info: installing component 'rust-std'
info: installing component 'rustc'
info: installing component 'rustfmt'
info: default toolchain set to 'stable-x86_64-apple-darwin'

  stable-x86_64-apple-darwin installed - rustc 1.65.0 (897e37553 2022-11-02)

Rust is installed now. Great!

To get started you may need to restart your current shell.
This would reload your PATH environment variable to include
Cargo's bin directory ($HOME/.cargo/bin).

To configure your current shell, run:
source "$HOME/.cargo/env"
```

### PATH を通す (fish-shell 利用者のみ)

`rustup-init` の実行結果を見て頂くと、解るとおり "sh", "bash", "zsh" はに関連するファイルには PATH を通す処理がありますが `fish-shell` を利用している方は設定が必要です

```shell:~/.config/fish/config.fish
if test -d ~/.cargo
    fish_add_path $HOME/.cargo/bin
end
```

https://fishshell.com/docs/current/cmds/fish_add_path.html

https://github.com/rust-lang/rustup/issues/478#issuecomment-1288194464


### 必要な toolchain を取得する

今回は、 `rust-analyzer` を設定します。
必要に応じて toolchain を取得しましょう

```zsh
rustup component add rust-analyzer
```

https://github.com/rust-lang/rust-analyzer

https://rust-analyzer.github.io/

## Reference

https://rust-lang.github.io/rustup/index.html