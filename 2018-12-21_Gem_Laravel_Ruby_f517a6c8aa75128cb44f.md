<!--
title:   Laravelを使ったことがあるならわかる､CollectionをRubyに移植しながらGemの作り方を学ぶ
tags:    Gem,Laravel,Ruby
id:      f517a6c8aa75128cb44f
private: false
-->
## はじめに
[Ateam Lifestyle x cyma Advent Calendar 2018](https://qiita.com/advent-calendar/2018/ateam-lifestyle-cyma) 24日目は、株式会社エイチームライフスタイル エンジニア @mziyut が担当します。


今回は､PHPを利用していると多くの場で見かける､｢Love beautiful code? We do too. ｣でお馴染み[Laravel](https://laravel.com/)
そのLaravelに存在する[Collections](https://laravel.com/docs/5.7/collections)をRubyでも使えるようGemを作成して行こうと思います｡👏

この投稿から、PHPerのRubyへの敷居を下げることと、RubyistのGem作成の手助けになり、
読んでくださった方々がGemの作り方を学び、OSSへの貢献への第一歩となればと思ってます。


## Collectionsについて

[はじめに](#はじめに)でも触れましたが、[Collections](https://laravel.com/docs/5.7/collections)を今回はRubyでも使えるようにしていきます。

Laravelについては割愛したいので、こちらの投稿を参照してみてください。
[Laravel入門-Laravelとは？特徴は？環境構築は？ - Qiita](https://qiita.com/torisankanasan/items/e7fb97e209d770970800)


今回フォーカスする[Collections](https://laravel.com/docs/5.7/collections)は、引数渡される配列や、オブジェクト等の定義されている値を
簡単に扱えるようにしてくれるwrapperのようなものです。

ちなみに､JavaScriptではすでにnpmに[collect.js - npm](https://www.npmjs.com/package/collect.js)として公開されています｡
今回は、Rubyのため Hash、Object、Arrayの3種類に対応しようと検討中です。

Collectionsの実行例を理解して頂きやすい、`all()`と`sum()`だけ記載します。
その他、methodについては、[Collections - AvailableMethods](https://laravel.com/docs/5.7/collections#available-methods)を参照してください。

```php:sample.php
/**
 * 配列の場合
 */
$collection = new Collection([1,2,3]);
echo $collection->all();
// [1,2,3]
echo $collection->sum();
// 6

/**
 * オブジェクトの場合
 */
$obj1 = new stdClass();
$obj1->number = 1;

$obj2 = new stdClass();
$obj2->number = 2;

$array = [];
$array[] = $obj1;
$array[] = $obj2;

$collection = new Collection($array);
echo $collection->sum('number');
// 3
```

## Gemを作成

+ 以下手順でGemを作成､公開までを行います｡
    + [1. Gemの雛形を作成](#1-gemの雛形を作成)
    + [2. collection_rb.gemspecの更新](#2-collection_rbgemspecの更新)
    + [3. Gemに機能を実装](#3-gemに機能を実装)

### 1. Gemの雛形を作成
+ まずGemの雛形を作成しましょう  
今回は､rspecも使いたかったため､ `-t` オプションをつけてコマンドを実行しました｡
    + `bundle gem`の細かなオプションについては[こちら](https://bundler.io/v1.17/bundle_gem.html)を参照ください

```sh
$ bundle gem collection_rb -t
Creating gem 'collection_rb'...
MIT License enabled in config
Code of conduct enabled in config
      create  collection_rb/Gemfile
      create  collection_rb/.gitignore
      create  collection_rb/lib/collection_rb.rb
      create  collection_rb/lib/collection_rb/version.rb
      create  collection_rb/collection_rb.gemspec
      create  collection_rb/Rakefile
      create  collection_rb/README.md
      create  collection_rb/bin/console
      create  collection_rb/bin/setup
      create  collection_rb/.travis.yml
      create  collection_rb/.rspec
      create  collection_rb/spec/spec_helper.rb
      create  collection_rb/spec/collection_rb_spec.rb
      create  collection_rb/LICENSE.txt
      create  collection_rb/CODE_OF_CONDUCT.md
Initializing git repo in /Users/mziyut/Workspace/github.com/mziyut/collection_rb
```

+ コンソールにも出ている通り､`collection_rb`ディレクトリが作成され､  
中にGemを作成する上で必要なものが一通り作成されます｡
+ また､`collection_rb`ディレクトリ以下は､  
git レポジトリとして､イニシャライズされ今回生成されたファイルがすべてステージに上がっています｡

```sh
$ cd collection_rb
$ ls -la
total 72
drwxr-xr-x  15 mziyut  staff   480 Dec 15 09:31 .
drwxr-xr-x  13 mziyut  staff   416 Dec 15 09:31 ..
drwxr-xr-x  10 mziyut  staff   320 Dec 15 09:31 .git
-rw-r--r--   1 mziyut  staff    87 Dec 15 09:31 .gitignore
-rw-r--r--   1 mziyut  staff    31 Dec 15 09:31 .rspec
-rw-r--r--   1 mziyut  staff    88 Dec 15 09:31 .travis.yml
-rw-r--r--   1 mziyut  staff  2381 Dec 15 09:31 CODE_OF_CONDUCT.md
-rw-r--r--   1 mziyut  staff    98 Dec 15 09:31 Gemfile
-rw-r--r--   1 mziyut  staff  1077 Dec 15 09:31 LICENSE.txt
-rw-r--r--   1 mziyut  staff  1553 Dec 15 09:31 README.md
-rw-r--r--   1 mziyut  staff   117 Dec 15 09:31 Rakefile
drwxr-xr-x   4 mziyut  staff   128 Dec 15 09:31 bin
-rw-r--r--   1 mziyut  staff  1426 Dec 15 09:31 collection_rb.gemspec
drwxr-xr-x   4 mziyut  staff   128 Dec 15 09:31 lib
drwxr-xr-x   4 mziyut  staff   128 Dec 15 09:31 spec
```

+ 雛形ができたことを確認できたため､ひとまずコミットし､リモートにプッシュておきましょう

```sh
$ git commit -m "creating gem 'collection_rb'"
[master (root-commit) d137bc9] creating gem 'collection_rb'
 15 files changed, 213 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 .rspec
 create mode 100644 .travis.yml
 create mode 100644 CODE_OF_CONDUCT.md
 create mode 100644 Gemfile
 create mode 100644 LICENSE.txt
 create mode 100644 README.md
 create mode 100644 Rakefile
 create mode 100755 bin/console
 create mode 100755 bin/setup
 create mode 100644 collection_rb.gemspec
 create mode 100644 lib/collection_rb.rb
 create mode 100644 lib/collection_rb/version.rb
 create mode 100644 spec/collection_rb_spec.rb
 create mode 100644 spec/spec_helper.rb
$ git remote add origin git@github.com:mziyut/collection_rb.git
$ git push -u origin master
git push -u origin master
Enumerating objects: 21, done.
Counting objects: 100% (21/21), done.
Delta compression using up to 4 threads
Compressing objects: 100% (18/18), done.
Writing objects: 100% (21/21), 5.17 KiB | 331.00 KiB/s, done.
Total 21 (delta 0), reused 0 (delta 0)
To github.com:mziyut/collection_rb.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

### 2. collection_rb.gemspecの更新

+ Gemの雛形を作成した際に､`TODO`と記載されている以下の部分を変更しましょう｡
     + spec.summary
     + spec.description
     + spec.homepage

```rb:collection_rb.gemspec
# coding: utf-8
lib = File.expand_path('../lib', __FILE__)
$LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)
require 'collection_rb/version'

Gem::Specification.new do |spec|
  spec.name          = "collection_rb"
  spec.version       = CollectionRb::VERSION
  spec.authors       = ["Yuta Mizui"]
  spec.email         = ["me@mziyut.com"]

  spec.summary       = %q{TODO: Write a short summary, because Rubygems requires one.}
  spec.description   = %q{TODO: Write a longer description or delete this line.}
  spec.homepage      = "TODO: Put your gem's website or public repo URL here."
  spec.license       = "MIT"

  # Prevent pushing this gem to RubyGems.org. To allow pushes either set the 'allowed_push_host'
  # to allow pushing to a single host or delete this section to allow pushing to any host.
  if spec.respond_to?(:metadata)
    spec.metadata['allowed_push_host'] = "TODO: Set to 'http://mygemserver.com'"
  else
    raise "RubyGems 2.0 or newer is required to protect against public gem pushes."
  end

  spec.files         = `git ls-files -z`.split("\x0").reject { |f| f.match(%r{^(test|spec|features)/}) }
  spec.bindir        = "exe"
  spec.executables   = spec.files.grep(%r{^exe/}) { |f| File.basename(f) }
  spec.require_paths = ["lib"]

  spec.add_development_dependency "bundler", "~> 1.12"
  spec.add_development_dependency "rake", "~> 10.0"
  spec.add_development_dependency "rspec", "~> 3.0"
end
```

### 3. Gemに機能を実装

+ それでは一通り準備が整ったため､機能を実装していきましょう｡

#### 依存パッケージのインストール

+ 普段開発を行うときと同じように､`bundle install`を実行しましょう

```sh
$ bundle install --path vendor/bundle
Fetching gem metadata from https://rubygems.org/...........
Fetching version metadata from https://rubygems.org/..
Resolving dependencies...
Using bundler 1.12.5
Using collection_rb 0.1.0 from source at `.`
Installing rspec-support 3.8.0
Installing diff-lcs 1.3
Installing rake 10.5.0
Installing rspec-expectations 3.8.2
Installing rspec-core 3.8.0
Installing rspec-mocks 3.8.0
Installing rspec 3.8.0
Bundle complete! 4 Gemfile dependencies, 9 gems now installed.
Bundled gems are installed into ./vendor/bundle.
```

#### 実装の検討

+ 今回は､[collections#method-all](https://laravel.com/docs/5.7/collections#method-all)を実装していきます｡
  + 引数に､配列､ハッシュ､オブジェクト等を予め渡しておき､`all`をcallすると渡しておいた値をそのまま返却します｡

##### PHPでの動作イメージは以下となります

```php:sample.php
$collection = new Collection([1,2,3]);
$collection->all();
// => [1, 2, 3]
```

##### 同じふるまいをrubyで実現させた場合の動作イメージは以下となります

```rb:sample.rb
collection = CollectionRb::Collection.new([1,2,3])
collection.all
# => [1, 2, 3]
```

#### テストの作成

+ それではある程度実装のイメージができたところでテストを書いていきましょう｡

```rb:spec/collection_rb_spec.rb
require 'spec_helper'

describe CollectionRb do
  it 'has a version number' do
    expect(CollectionRb::VERSION).not_to be nil
  end

  describe '.all' do
    let(:collection_rb) { CollectionRb::Collection.new(params)  }
    subject { collection_rb.all }
    describe '渡される値が配列の場合' do
      context '配列の中身がIntの場合' do
        let(:params) { [1,2,3,4,5] }
        it { is_expected.to eq params  }
      end
      context '配列の中身がObjectの場合' do
        let(:params) do
          objects = []
          objects << Object.new
          objects << Object.new
          objects
        end
        it { is_expected.to eq params  }
      end
    end
    context '渡される値がHashの場合' do
      let(:params) { { a: 1, b: 2, c: 3 } }
      it { is_expected.to eq params }
    end
  end
end
```

#### ロジックを記述しましょう

+ テストを書いたので､肝心なロジックを作っていきます
+ ただ､今回は値をただ返却するだけなのでとてもシンプルです｡

```rb:lib/collection_rb.rb
require "collection_rb/version"

module CollectionRb
  class Collection
    def initialize(params)
      @params = params
    end

    def all
      @params
    end
  end
end
```

## Gemを公開

### Rubygemsに公開

+ まだまだ､機能は足りていませんが､作成したものは[Rubygems](https://rubygems.org/)に公開してみましょう
+ 公開するには､以下コマンドを実行すると公開することができます｡
    + マシンスペックによって時間がかかります

```sh
$ bundle exec rake release
```

## おわりに

+ 今回は､Gemの作成から公開までを書きました｡
+ Gemを作成するのは非常に簡単ため､みなさんもぜひ作ってみてはいかがでしょうか｡
+ また､今回作成出来なかった`.all`以外の[105の機能]((https://laravel.com/docs/5.7/collections#available-methods))についてはお正月休みのときに全部作りきろうと思ってます😇
   + [mziyut/collection_rb](https://github.com/mziyut/collection_rb)


[Ateam Lifestyle x cyma Advent Calendar 2018](https://qiita.com/advent-calendar/2018/ateam-lifestyle-cyma) 25日目アドベントカレンダー最終日は @sakupa80 さんです! 

## お知らせ

エイチームグループでは、一緒に働けるチャレンジ精神旺盛な仲間を募集しています。興味を持たれた方はぜひエイチームグループ採用サイトを御覧ください。
https://www.a-tm.co.jp/recruit/