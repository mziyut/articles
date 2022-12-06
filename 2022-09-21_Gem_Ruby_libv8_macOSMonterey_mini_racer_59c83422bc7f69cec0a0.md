<!--
title:   macOS Monterey (x86_64-darwin-21) で libv8 の install を行う方法
tags:    Gem,Ruby,libv8,macOSMonterey,mini_racer
id:      59c83422bc7f69cec0a0
private: false
-->
## はじめに

macOS Monterey (x86_64-darwin-21) 環境下で [mini_racer] が依存する [libv8] gem を install する必要があり対処方法をまとめました

## TL;DR

- macOS Monterey (`x86_64-darwin-21`) 環境下では "libv8" gem の install は出来ない
- 前 macOS Big Sur (`x86_64-darwin-20`) 用のファイルを取得し patch をあてセットアップを行うと簡単

## なぜ macOS Monterey で簡単に install できないのか

[All versions of libv8] を確認してみると `x86_64-darwin-21` 用の binary が提供されていません。
binary が提供されていない = source から build する必要があります

[libv8] の README に記載されている Platform は以下のみとなり `x86_64-darwin-21` 環境下だと簡単に install することは出来ません

- x86_64-darwin-19
- x86_64-darwin-18
- x86_64-darwin-17
- x86_64-linux
- x86-linux

また、 README には `x86_64-darwin-20` の記載がありませんが `x86_64-darwin-20` 用 binary は提供されており `x86_64-darwin-20` 環境だと install には成功します
本件に関する Issue は作成されていますが、解決策として提示されているものは [mini_racer] を更新することで [libv8] の使用を [libv8-node] に変更することを推奨されています

https://github.com/rubyjs/libv8/issues/318


## [libv8] と [libv8-node] について

[libv8] と [libv8-node] (と [therubyracer]) については以下記事を参照ください

https://qiita.com/mziyut/items/f0037b94ed707bbb3ea0


## install する方法

:::note warn
差分が生じる `Gemfile.lock` をバージョン管理化や本番デプロイに含めることをは推奨しません
差分を含める、本番デプロイを行う場合は十分に注意して実行するようにしてください
:::


行う作業は以下となる

- Issue の comment に記載されている shell script を取得する
- shell script の permission を変更し、実行する
- Gemfile.lock に `libv8 (8.4.255.0-x86_64-darwin-21)` を追記する

### Issue の comment に記載されている shell script を取得する

以下 [libv8] repository に作成されている Issue 内にある shell script を取得します
[Missing macOS Monterey build (x86_64-darwin-21) · Issue #318 · rubyjs/libv8 - ksylvest commented](https://github.com/rubyjs/libv8/issues/318#issuecomment-956466977)
取得した shell script を `gem-install-libv8-darwin-20.sh` として local に保存します

### shell script の permission を変更し、実行する

先程保存した shell script の permissinon を変更します

```sh
$ chmod +x gem-install-libv8-darwin-20.sh
```

permission の変更が完了したら shell script を実行します
実行を行うと `libv8-8.4.255.0-x86_64-darwin-20.gemspec` の download を行い patch 適用後 `libv8-8.4.255.0-x86_64-darwin-21.gemspec` の install が実行されます

```sh
$ ./gem-install-libv8-darwin-20.sh
 *** detecting
/path_to_rbenv/rbenv/versions/x.x.x/lib/ruby/gems/x.x.x
x86_64-darwin-21
x86_64-darwin
libv8 8.4.255.0: x86_64-darwin-20 => x86_64-darwin-21
 *** downloading
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 17.0M  100 17.0M    0     0  11.3M      0  0:00:01  0:00:01 --:--:-- 11.3M
 *** cleaning
// 一部省略...
 *** installing
Successfully installed libv8-8.4.255.0-x86_64-darwin-20
Building YARD (yri) index for libv8-8.4.255.0-x86_64-darwin-20...
Done installing documentation for libv8 after 0 seconds
1 gem installed
 *** patching
/path_to_rbenv/redeem/env/rbenv/versions/x.x.x/lib/ruby/gems/x.x.x/specifications/libv8-8.4.255.0-x86_64-darwin-20.gemspec -> /path_to_rbenv/rbenv/versions/x.x.x/lib/ruby/gems/x.x.x/specifications/libv8-8.4.255.0-x86_64-darwin-21.gemspec
/path_to_rbenv/rbenv/versions/x.x.x/lib/ruby/gems/x.x.x/doc/libv8-8.4.255.0-x86_64-darwin-20 -> /path_to_rbenv/redeem/env/rbenv/versions/x.x.x/lib/ruby/gems/x.x.x/doc/libv8-8.4.255.0-x86_64-darwin-21
/path_to_rbenv/redeem/env/rbenv/versions/x.x.x/lib/ruby/gems/x.x.x/gems/libv8-8.4.255.0-x86_64-darwin-20 -> /path_to_rbenv/redeem/env/rbenv/versions/x.x.x/lib/ruby/gems/x.x.x/gems/libv8-8.4.255.0-x86_64-darwin-21
/path_to_rbenv/redeem/env/rbenv/versions/x.x.x/lib/ruby/gems/x.x.x/cache/libv8-8.4.255.0-x86_64-darwin-20.gem -> /path_to_rbenv/redeem/env/rbenv/versions/x.x.x/lib/ruby/gems/x.x.x/cache/libv8-8.4.255.0-x86_64-darwin-21.gem
 *** result
// 一部省略...
```

実行が完了したら `libv8 (8.4.255.0 x86_64-darwin-21)` が install されていることを確認します

```sh
$ gem list | grep libv8
libv8 (8.4.255.0 x86_64-darwin-21)
```

### Gemfile.lock に `libv8 (8.4.255.0-x86_64-darwin-21)` を追記する

macOS Monterey (`x86_64-darwin-21`) 環境下で `libv8` の install が完了したら `Gemfile.lock` に定義を追記します

```diff_ruby
  libv8 (8.4.255.0)
  libv8 (8.4.255.0-universal-darwin-20)
  libv8 (8.4.255.0-x86_64-darwin-20)
+ libv8 (8.4.255.0-x86_64-darwin-21)
  libv8 (8.4.255.0-x86_64-linux)
```

定義追加後 `bundle install` を実行し適切に Gem が認識されるか確認してください


## Reference

https://github.com/rubyjs/libv8/issues/318

https://github.com/rubyjs/libv8

https://rubygems.org/gems/libv8

[All versions of libv8]: https://rubygems.org/gems/libv8/versions
[mini_racer]: https://rubygems.org/gems/mini_racer
[libv8]: https://rubygems.org/gems/libv8
[libv8-node]: https://rubygems.org/gems/libv8-node
[therubyracer]: https://rubygems.org/gems/therubyracer