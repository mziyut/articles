<!--
title:   Bundler の Platform 一覧 （Mac OS / Linux版）
tags:    QiitaEngineerFesta2022,Ruby,amd64,arm64,bundler
id:      d75222fee131457aaa37
private: false
-->
## はじめに

- Bundler 2.2.x から 適切なプラットフォームを `Gemfile.lock` に記載する必要があった
- すべての環境をすぐに用意、再現できるマシンが複数手元にないため Platform 一覧を作成した
    - Mac OS は新しい OS が公開後更新します

## Platform一覧
- Linux, Mac 別に記載します
    - 32bit 環境や Windows マシン は手元に無いため省略しています
- 以下に記載する Platform 名を `bundle lock --add-platform <Platform名>` コマンド実行時に渡すことで、実行環境の追加できます

### Linux

| OS | x84_64 | arm64 |
|:--|:--|:--|
| Linux | x86_64-linux | arm64-linux |

### Mac OS (darwin)
- Mac OS は新しい OS が公開されたら Platform 指定が変更になるため、アップグレード後追記します

| OS | x84_64 | arm64 |
|:--|:--|:--|
| macOS Mojave 10.14.x (darwin-18) | x86_64-darwin-18 | - |
| macOS Catalina 10.15.x (darwin-19) | x86_64-darwin-19 | - |
| macOS Big Sur 11.x (darwin-20) | x86_64-darwin-20 | arm64-darwin-20 |
| macOS Monterey 12.x (darwin-21) | x86_64-darwin-21 | arm64-darwin-21 |
| macOS Ventura (darwin-22) | x86_64-darwin-22 | arm64-darwin-22 |

## Reference

https://gist.github.com/GolgothaX/4540102

https://github.com/rubygems/rubygems/blob/master/lib/rubygems/platform.rb#L14-L18

https://github.com/discourse/discourse/blob/main/Gemfile.lock

https://en.wikipedia.org/wiki/MacOS#Release_history